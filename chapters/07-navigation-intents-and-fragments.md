# 7. Intents, fragments, and navigation

[Back to notes index](../README.md)

| [Previous: Events, forms, and validation](06-events-forms-and-validation.md) | [Notes index](../README.md) | [Next: Lists with RecyclerView and adapters](08-recyclerview-and-adapters.md) |
|:--|:--:|--:|

## An Intent describes an action

An `Intent` carries a request for another Android component to do something. It can name a component directly, describe an action for another app to handle, and carry small pieces of data in extras.

| Intent type | How the target is chosen | Common use |
| --- | --- | --- |
| Explicit | The code names the exact component class. | Open a screen in the same app. |
| Implicit | Android finds an app that supports the requested action and data. | Share text or open a web link in another app. |

For a screen inside my app, I use an explicit intent. Inside a click callback, the first activity creates the intent and sends the selected note's ID as an extra:

```java
import android.content.Intent;

Intent intent = new Intent(this, NoteDetailActivity.class);
intent.putExtra(NoteDetailActivity.EXTRA_NOTE_ID, noteId);
startActivity(intent);
```

The destination reads the extra from its incoming intent:

```java
import android.os.Bundle;

import androidx.fragment.app.FragmentActivity;

public class NoteDetailActivity extends FragmentActivity {
    public static final String EXTRA_NOTE_ID = "com.example.studyapp.NOTE_ID";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_note_detail);

        long noteId = getIntent().getLongExtra(EXTRA_NOTE_ID, -1L);
        if (noteId == -1L) {
            finish();
            return;
        }

        // Load the note identified by noteId.
    }
}
```

The activity layout can provide a simple destination view:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/note_detail_title"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:padding="24dp"
    android:text="@string/note_detail_placeholder"
    android:textSize="20sp" />
```

An ID is usually a better value to pass than a whole database record. The destination can load the current record from the data layer. Extras are for small values needed to identify or initialize the destination, not for large objects or long-lived state.

Declare the destination activity in `AndroidManifest.xml`. An activity used only inside the app can stay unavailable to other apps:

```xml
<activity xmlns:android="http://schemas.android.com/apk/res/android"
    android:name=".NoteDetailActivity"
    android:exported="false" />
```

An activity with an intent filter that should be launched from outside the app needs a deliberate `android:exported` value. On current Android targets, components with intent filters must declare this value. Exposing an activity means other apps can attempt to start it, so validate incoming actions and data as untrusted input.

## Implicit intents use another app

For example, an app can ask Android to share a short text value. Android shows a chooser with apps that can accept it:

```java
import android.content.Intent;

Intent shareIntent = new Intent(Intent.ACTION_SEND);
shareIntent.setType("text/plain");
shareIntent.putExtra(Intent.EXTRA_TEXT, "I am reviewing Android intents.");

Intent chooser = Intent.createChooser(shareIntent, getString(R.string.share_note));
startActivity(chooser);
```

An implicit intent names an action, MIME type, and sometimes a `Uri`. It does not name a specific app. The system matches it against apps that have declared compatible intent filters. Use `Intent.createChooser()` when the person should select the receiving app.

When an activity receives an implicit intent, validate its action, data scheme, MIME type, and extras before using them. An intent filter describes what a component can receive. It is not a security boundary and does not make input trustworthy.

## Get an activity result

The Activity Result APIs register a callback for an operation such as selecting an image. The launcher can be a field on a `ComponentActivity` or `Fragment` subclass:

```java
import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;

private final ActivityResultLauncher<String> selectImage =
        registerForActivityResult(
                new ActivityResultContracts.GetContent(),
                uri -> {
                    if (uri != null) {
                        // Read or display the selected image URI.
                    }
                }
        );
```

A button can launch the registered contract:

```java
binding.selectImageButton.setOnClickListener(view -> selectImage.launch("image/*"));
```

Register the callback every time the activity or fragment is created, in a stable order. Registering it only inside a click callback can lose a pending result if Android recreates the screen while another app is open. For the same reason, save any extra state needed to process the result separately from the callback. The Activity Result APIs replace the older `startActivityForResult()` and `onActivityResult()` pattern for new code.

## An Activity hosts a screen, a Fragment is a reusable part

An `Activity` is an app component that Android can launch. A `Fragment` represents part of a UI and its behavior inside a host, usually an activity. A fragment has its own lifecycle, but it depends on its host and cannot appear on its own.

Fragments are useful when a screen needs reusable sections, a list and detail layout, or a navigation area that changes while the activity stays in place. A separate activity can be simpler for a distinct destination or a task with its own window behavior. I avoid adding fragments just to split a very small screen into more files.

The fragment library is AndroidX. The app module needs the Fragment dependency, and the versions should follow the project's AndroidX setup. `FragmentActivity` is a host that provides `getSupportFragmentManager()`:

```java
import android.os.Bundle;

import androidx.fragment.app.FragmentActivity;

public class MainActivity extends FragmentActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        if (savedInstanceState == null) {
            getSupportFragmentManager()
                    .beginTransaction()
                    .setReorderingAllowed(true)
                    .replace(R.id.fragment_container, NoteListFragment.class, null)
                    .commit();
        }
    }
}
```

The `savedInstanceState == null` check prevents the app from adding a second initial fragment after a configuration change. The `FragmentManager` restores its fragments and back stack as the activity is recreated.

The host layout supplies a container for the fragment:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/fragment_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

The list fragment needs a layout containing the button looked up by `R.id.open_detail`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <Button
        android:id="@+id/open_detail"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/open_note" />

</LinearLayout>
```

Add these values to `res/values/strings.xml`:

```xml
<resources>
    <string name="share_note">Share note</string>
    <string name="open_note">Open a saved note</string>
    <string name="note_detail_placeholder">Selected note details appear here</string>
</resources>
```

The fragment creates its view in `onCreateView()`. The fragment instance and its view have related but separate lifecycles. The view can be destroyed while the fragment remains on the back stack, so view references must not be used after `onDestroyView()`.

```java
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class NoteListFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(
            @NonNull LayoutInflater inflater,
            @Nullable ViewGroup container,
            @Nullable Bundle savedInstanceState
    ) {
        return inflater.inflate(R.layout.fragment_note_list, container, false);
    }

    @Override
    public void onViewCreated(
            @NonNull View view,
            @Nullable Bundle savedInstanceState
    ) {
        super.onViewCreated(view, savedInstanceState);
        Button openDetail = view.findViewById(R.id.open_detail);
        openDetail.setOnClickListener(ignored -> openNoteDetail());
    }

    private void openNoteDetail() {
        Bundle arguments = new Bundle();
        arguments.putLong(NoteDetailFragment.ARG_NOTE_ID, 42L);

        getParentFragmentManager()
                .beginTransaction()
                .setReorderingAllowed(true)
                .replace(R.id.fragment_container, NoteDetailFragment.class, arguments)
                .addToBackStack(null)
                .commit();
    }
}
```

The details screen reads the argument when its fragment is created:

```java
import android.os.Bundle;
import android.view.View;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class NoteDetailFragment extends Fragment {
    public static final String ARG_NOTE_ID = "note_id";

    public NoteDetailFragment() {
        super(R.layout.fragment_note_detail);
    }

    @Override
    public void onViewCreated(
            @NonNull View view,
            @Nullable Bundle savedInstanceState
    ) {
        super.onViewCreated(view, savedInstanceState);
        long noteId = requireArguments().getLong(ARG_NOTE_ID, -1L);

        // Load and display the note identified by noteId.
    }
}
```

Its `fragment_note_detail.xml` can start with the same simple placeholder view:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/note_detail_content"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:padding="24dp"
    android:text="@string/note_detail_placeholder"
    android:textSize="20sp" />
```

Use a no-argument fragment constructor and pass initial values through a `Bundle`. The `FragmentManager` may recreate a fragment after process or configuration changes, so it must be able to instantiate it again. Avoid storing a direct reference to an activity or another fragment as a way to pass data.

## Understand the FragmentManager back stack

`FragmentManager` applies fragment changes in a `FragmentTransaction`. `replace()` removes the current fragment from the container and shows another one. `commit()` schedules the transaction on the main thread; it does not run the change synchronously.

The initial list screen was committed without `addToBackStack()`, so it is the root. The detail screen was added with `addToBackStack(null)`, so Back reverses that transaction and returns to the list. If every screen is added to the back stack, Back can lead to an empty container or an unexpected path. Decide which destinations form the root of the flow.

Use `setReorderingAllowed(true)` on transactions, particularly those on the back stack. Do not use `commitNow()` for a transaction that you also add to the back stack. Let the manager restore fragments rather than unconditionally creating new instances after recreation.

For a small flow, `FragmentManager` transactions can be enough. A larger app with defined destinations, deep links, and back behavior may benefit from the AndroidX Navigation component. It keeps destinations and transitions in a navigation graph, and can work with activities and fragments. Either way, a navigation action should carry only the information the destination needs, such as a database ID.

## Pass information without coupling screens

Use fragment arguments for values needed when the destination first appears. To return a one-time result, such as a selected filter, the Fragment Result API lets one fragment send a `Bundle` through a `FragmentManager`:

```java
// In the receiving fragment, register while creating the fragment.
getParentFragmentManager().setFragmentResultListener(
        "selected_topic",
        this,
        (requestKey, result) -> {
            String topic = result.getString("topic");
            // Update this screen with the selected topic.
        }
);
```

```java
// In the fragment that returns a selection.
Bundle result = new Bundle();
result.putString("topic", selectedTopic);
getParentFragmentManager().setFragmentResult("selected_topic", result);
```

The Fragment Result API is available in AndroidX Fragment 1.3.0 and later. The receiving listener runs when its lifecycle is at least `STARTED`. Use a shared `ViewModel` when two screens need to observe ongoing shared UI state. These approaches avoid keeping direct references between fragment instances. The next notes chapter covers `ViewModel` and `LiveData`.

## Notes to remember

- Use explicit intents for a known component in the same app and implicit intents for an action another app can handle.
- Validate incoming intent data, even when the destination belongs to the same app.
- Pass small IDs or values through extras and arguments. Load larger or persistent data from the app's data layer.
- Register Activity Result callbacks with the activity or fragment lifecycle and keep registration order stable.
- A fragment's view lifecycle can end before the fragment instance does.
- `addToBackStack()` makes a transaction reversible when the user presses Back.
- Use arguments, Fragment Results, or a shared `ViewModel` instead of direct fragment references.

## References

- [Intents and intent filters](https://developer.android.com/guide/components/intents-filters)
- [Get a result from an activity](https://developer.android.com/training/basics/intents/result)
- [Fragment manager](https://developer.android.com/guide/fragments/fragmentmanager)
- [Fragment transactions](https://developer.android.com/guide/fragments/transactions)
- [Communicate with fragments](https://developer.android.com/guide/fragments/communicate)
- [Navigation](https://developer.android.com/guide/navigation)

