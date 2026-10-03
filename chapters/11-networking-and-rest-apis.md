# 11. Networking, REST APIs, and JSON

[Back to notes index](../README.md)

| [Previous: Local data with Room and preferences](10-local-data-room-and-preferences.md) | [Notes index](../README.md) | [Next: Background work and notifications](12-background-work-and-notifications.md) |
|:--|:--:|--:|

## An HTTP request and response

A REST API exposes resources through URLs. An app sends an HTTP request, and the server replies with a status code, headers, and usually a body. The request method describes the kind of operation:

| Method | Common meaning |
| --- | --- |
| `GET` | Read a resource. |
| `POST` | Create a resource or submit an operation. |
| `PUT` | Replace a resource. |
| `PATCH` | Change selected fields. |
| `DELETE` | Remove a resource. |

The exact behavior still depends on the API's contract. Check the API documentation for required paths, headers, request fields, authentication, and response formats.

HTTP status codes help classify the result. A `2xx` status means the request succeeded. `200` commonly carries a response body, `201` means a resource was created, and `204` means success with no response body. `4xx` usually means the request or its permissions need attention. `5xx` means the server failed while handling it. Check the code before treating a response body as success data.

## Allow network access in the manifest

Declare the `INTERNET` permission in `AndroidManifest.xml`, outside the `<application>` element:

```xml
<uses-permission xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="android.permission.INTERNET" />
```

`INTERNET` is a normal permission granted at install time, so it does not need a runtime permission dialog. Add `ACCESS_NETWORK_STATE` only if the app needs to inspect connectivity. A connectivity check is only a hint because the network can change immediately after the check.

Use HTTPS for API traffic so the connection is encrypted. Do not send passwords, access tokens, or personal details over cleartext HTTP. Do not place private API credentials directly in app source code; values bundled in an installed app can be extracted.

## Read a JSON response with HttpURLConnection

`HttpURLConnection` is part of Java and can make an HTTP request without an extra network library. The endpoint below is a placeholder. Replace it with an HTTPS endpoint and response contract that you control or are allowed to call.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;

public class PostApi {
    public String getPost(int postId) throws IOException {
        HttpURLConnection connection = null;

        try {
            URL url = new URL("https://api.example.com/posts/" + postId);
            connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setConnectTimeout(10_000);
            connection.setReadTimeout(10_000);
            connection.setRequestProperty("Accept", "application/json");

            int statusCode = connection.getResponseCode();
            InputStream responseStream = statusCode >= 200 && statusCode < 300
                    ? connection.getInputStream()
                    : connection.getErrorStream();

            StringBuilder responseBody = new StringBuilder();
            if (responseStream != null) {
                try (BufferedReader reader = new BufferedReader(
                        new InputStreamReader(responseStream, StandardCharsets.UTF_8)
                )) {
                    String line;
                    while ((line = reader.readLine()) != null) {
                        responseBody.append(line);
                    }
                }
            }

            if (statusCode < 200 || statusCode >= 300) {
                throw new IOException("The request failed with HTTP " + statusCode);
            }

            return responseBody.toString();
        } finally {
            if (connection != null) {
                connection.disconnect();
            }
        }
    }
}
```

The connect timeout limits how long the app waits to establish a connection. The read timeout limits how long it waits for data. The response stream is closed with try-with-resources, and `disconnect()` releases the connection. Error responses use `getErrorStream()`, so the code can read an error body instead of assuming every response is successful. This sample reports the status code in its exception. A real API layer can parse a documented error body for useful diagnostics. A missing body remains an empty string, allowing the status code to be handled first.

For a `204` response, an API may have no body. Code that accepts such a response should branch on the status and avoid trying to parse an empty body as JSON.

## Keep the request off the main thread

Networking can wait on DNS, a server, or a weak connection. Running it on Android's main thread can freeze the screen and throw `NetworkOnMainThreadException`. Use a background executor for the request and post a display update to the main thread:

```java
import android.os.Handler;
import android.os.Looper;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.IOException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

private final ExecutorService networkExecutor = Executors.newSingleThreadExecutor();
private final Handler mainHandler = new Handler(Looper.getMainLooper());
private final PostApi postApi = new PostApi();

private void loadPost() {
    statusView.setText("Loading...");

    networkExecutor.execute(() -> {
        try {
            String json = postApi.getPost(1);
            JSONObject post = new JSONObject(json);
            String title = post.optString("title", "Untitled post");
            mainHandler.post(() -> statusView.setText(title));
        } catch (IOException | JSONException exception) {
            mainHandler.post(() -> statusView.setText("Could not load the post."));
        }
    });
}
```

This small example assumes the screen stays open until the response arrives. In a screen that can be left while a request is running, put the request in a repository and expose the result through the ViewModel. Then the activity or fragment observes state with its lifecycle owner instead of a background callback keeping a view reference.

Show a loading state while a request is running, a useful error state when it fails, and data only after a successful response. Do not show raw exception text to users. Keep enough detail in developer logs to diagnose an issue, but avoid logging passwords, tokens, or private response data.

## Understand the JSON shape

JSON has objects, arrays, strings, numbers, booleans, and `null`. An object uses named fields:

```json
{
  "id": 1,
  "title": "Activity lifecycle",
  "body": "Callbacks and saved state"
}
```

The `JSONObject` in the background example reads the `title` field. `optString()` returns a fallback when the field is missing. `getString()` is useful when the field is required, but it throws `JSONException` if the value is absent or has the wrong type. Validate required fields before using them.

An array is an ordered list of values. Read each element with `JSONArray`:

```java
JSONArray posts = responseObject.getJSONArray("items");
List<String> titles = new ArrayList<>();

for (int index = 0; index < posts.length(); index++) {
    JSONObject post = posts.getJSONObject(index);
    titles.add(post.optString("title", "Untitled post"));
}
```

Add imports for `org.json.JSONArray`, `org.json.JSONObject`, `java.util.ArrayList`, and `java.util.List` where this example is used. Android includes `org.json`, so this basic parser does not need another dependency. For a large API with many models, a typed client and serializer can reduce manual parsing, but they add libraries and setup.

## Send JSON in a request

A `POST` request usually sends a JSON body and declares its media type. `Accept` describes what the client can read. `Content-Type` describes what the request body contains.

```json
{
  "title": "Networking notes",
  "body": "Requests run outside the UI thread."
}
```

For an authenticated API, send the authorization header only over HTTPS and follow the service's token rules. Avoid placing tokens in URL query parameters because URLs can appear in logs. Use the HTTP method and idempotency rules documented by the service.

## Decide whether a networking library helps

`HttpURLConnection` is useful for understanding the request, response, status code, and stream handling. It also works for a small one-off request. A larger app can use an HTTP client such as OkHttp, or Retrofit when it needs typed API interfaces. These libraries add dependencies, so choose them when connection setup, cancellation, converters, or shared request behavior would otherwise be repeated.

Regardless of the client, the screen should not own raw socket work. Keep the API call in a data layer, return a clear result or state, and let the ViewModel expose it to the UI. A local Room cache can keep previously loaded content available when the network is unavailable.

## Network checks I keep

- Use the `INTERNET` manifest permission and HTTPS endpoints.
- Set connect and read timeouts so requests do not wait forever.
- Run all network and response parsing work away from the main thread.
- Check the HTTP status code before parsing a success response.
- Handle empty bodies, missing JSON fields, malformed JSON, timeouts, and server failures.
- Close streams and disconnect `HttpURLConnection` in a `finally` block.
- Keep authentication tokens and personal data out of source URLs and logs.
- Show loading, success, and error states, and do not update a destroyed screen.
- Treat a connectivity check as temporary information, not proof that a request will succeed.

## Practice: display a remote title

Use an HTTPS test endpoint that returns one JSON object with `id` and `title`. Add the manifest permission, request one record on a background executor, and show its title only after receiving a successful HTTP status. Try a missing host, an invalid record ID, malformed JSON, and a response with no body. Confirm that each case leaves the screen responsive and produces a useful state.

## Notes to remember

- REST APIs use HTTP methods to describe operations on resources.
- The status code is part of the response and must be checked.
- JSON must match the API's shape; missing or mistyped fields need handling.
- Android network calls belong off the main thread.
- Use HTTPS, timeouts, clear error states, and safe handling of credentials.
- A repository and ViewModel keep network work separate from the screen.

## References

- [Connect to the network](https://developer.android.com/develop/connectivity/network-ops/connecting)
- [Networking overview](https://developer.android.com/develop/connectivity/network-ops)
- [HttpURLConnection reference](https://developer.android.com/reference/java/net/HttpURLConnection)
- [JSONObject reference](https://developer.android.com/reference/org/json/JSONObject)


