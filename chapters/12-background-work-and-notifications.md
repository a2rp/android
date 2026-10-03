# 12. Background work and notifications

[Back to notes index](../README.md)

| [Previous: Networking, REST APIs, and JSON](11-networking-and-rest-apis.md) | [Notes index](../README.md) | [Next: Permissions, privacy, and device features](13-permissions-and-device-features.md) |
|:--|:--:|--:|

## Pick the API based on how the work should behave

Background work is work that should continue when the user is not looking at the screen. The right API depends on whether the work must be saved for later, must be visible while running, or must start at a precise time.

| Need | Android API to consider | Timing behavior |
| --- | --- | --- |
| Deferrable work that should finish reliably, such as uploading saved notes. | WorkManager | Runs when constraints and system scheduling allow. |
| Ongoing work the user can notice and control, such as music playback or an active route. | Foreground service | Runs with a persistent notification and service-type rules. |
| An action tied to an exact clock time, such as a user-set alarm. | `AlarmManager` | Exact alarms have additional restrictions and should be reserved for time-critical features. |

Use WorkManager for ordinary scheduled sync, retries, and work that should survive process restarts. It does not promise an exact start time. A foreground service is not a general-purpose way to keep every job alive; the work must be noticeable to the user and meet current service rules.

## Define work with WorkManager

WorkManager uses three main objects:

- A `Worker` contains the operation.
- A `WorkRequest` describes when and under what constraints it can run.
- `WorkManager` schedules the request.

Add the AndroidX WorkManager runtime using the project's dependency conventions. The Java examples use `androidx.work:work-runtime`.

The notification examples use AndroidX Core's `androidx.core:core` artifact for `NotificationCompat` and `NotificationManagerCompat`.

This worker represents uploading locally saved notes. `doWork()` already runs on a WorkManager background thread. It should return only after its work is done:

```java
import android.content.Context;

import androidx.annotation.NonNull;
import androidx.work.Worker;
import androidx.work.WorkerParameters;

import java.io.IOException;

public class UploadNotesWorker extends Worker {
    public UploadNotesWorker(
            @NonNull Context context,
            @NonNull WorkerParameters workerParameters
    ) {
        super(context, workerParameters);
    }

    @NonNull
    @Override
    public Result doWork() {
        try {
            // Read pending notes from local storage and upload them.
            uploadPendingNotes();
            return Result.success();
        } catch (IOException exception) {
            return Result.retry();
        }
    }

    private void uploadPendingNotes() throws IOException {
        // Call the repository or API client from the data layer.
    }
}
```

`Result.success()` marks the work complete. `Result.retry()` asks WorkManager to try again using its backoff policy. `Result.failure()` marks a permanent failure. Do not start a new thread inside `doWork()` and return immediately, because the worker would finish before that thread's work has completed.

Add a network constraint and enqueue unique work to avoid scheduling duplicate uploads:

```java
import android.content.Context;

import androidx.work.Constraints;
import androidx.work.ExistingWorkPolicy;
import androidx.work.NetworkType;
import androidx.work.OneTimeWorkRequest;
import androidx.work.WorkManager;

Constraints constraints = new Constraints.Builder()
        .setRequiredNetworkType(NetworkType.CONNECTED)
        .build();

OneTimeWorkRequest uploadRequest =
        new OneTimeWorkRequest.Builder(UploadNotesWorker.class)
                .setConstraints(constraints)
                .build();

WorkManager.getInstance(context).enqueueUniqueWork(
        "upload-study-notes",
        ExistingWorkPolicy.KEEP,
        uploadRequest
);
```

With `KEEP`, an unfinished request with the same name remains in the queue and a duplicate is ignored. Choose another `ExistingWorkPolicy` only when replacing or chaining the current work matches the feature.

Periodic work is useful for recurring sync, not for a clock deadline. WorkManager's minimum repeat interval is 15 minutes, and actual run times depend on constraints and system scheduling:

```java
import androidx.work.ExistingPeriodicWorkPolicy;
import androidx.work.PeriodicWorkRequest;
import androidx.work.WorkManager;

import java.util.concurrent.TimeUnit;

PeriodicWorkRequest syncRequest = new PeriodicWorkRequest.Builder(
        UploadNotesWorker.class,
        6,
        TimeUnit.HOURS
).build();

WorkManager.getInstance(context).enqueueUniquePeriodicWork(
        "periodic-study-note-sync",
        ExistingPeriodicWorkPolicy.KEEP,
        syncRequest
);
```

Input `Data` passed to a worker is intended for small values such as an ID or a mode. Keep large content in Room or files and pass a key that identifies it. Workers can be stopped if their constraints become unmet, so they should handle interruption and be safe to retry.

## Know when foreground work is appropriate

A foreground service is for work that is actively noticeable to the user, such as recording an exercise session, navigation, or media playback. It must display an ongoing notification. Starting one from the background is restricted, and current Android versions require a declared service type with matching permissions for the work it performs.

On Android 14 and higher, foreground services must declare an appropriate type, such as `location`, `mediaPlayback`, or `dataSync`, and the corresponding permission rules apply. Android 15 adds further limits to some service types. Check the current requirements for the exact type before adding a service. Prefer WorkManager or a purpose-built API when the work is deferrable or a dedicated system API fits better.

## Create a notification channel

Android 8.0 (API 26) and higher require notifications to use a channel. A channel has a stable ID, a user-visible name, and an importance level. Users can change its behavior in system settings, and the app cannot later change the channel's importance after it has been created.

```java
import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.content.Context;
import android.os.Build;

public final class StudyNotifications {
    public static final String CHANNEL_ID = "study_reminders";

    private StudyNotifications() {
    }

    public static void createChannel(Context context) {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.O) {
            return;
        }

        CharSequence name = "Study reminders";
        String description = "Reminders about saved study sessions";
        NotificationChannel channel = new NotificationChannel(
                CHANNEL_ID,
                name,
                NotificationManager.IMPORTANCE_DEFAULT
        );
        channel.setDescription(description);

        NotificationManager manager = (NotificationManager)
                context.getSystemService(Context.NOTIFICATION_SERVICE);
        manager.createNotificationChannel(channel);
    }
}
```

Create the channel before posting a notification. Calling `createChannel()` more than once is safe because an existing channel is not recreated with different behavior. In a finished app, keep the channel name and description in string resources.

## Request notification permission when needed

Android 13 (API 33) and higher require the `POST_NOTIFICATIONS` runtime permission for ordinary notifications. Add it to the manifest and ask at a moment that makes sense for the feature, such as when the user turns on reminders. Do not prompt immediately on every app launch.

```xml
<uses-permission xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="android.permission.POST_NOTIFICATIONS" />
```

Use the Activity Result API from the activity to request the permission:

```java
import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Build;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.core.content.ContextCompat;

private final ActivityResultLauncher<String> notificationPermission =
        registerForActivityResult(
                new ActivityResultContracts.RequestPermission(),
                granted -> {
                    if (granted) {
                        StudyNotifications.showStudyReminder(this);
                    } else {
                        // Keep the feature usable without notifications.
                    }
                }
        );

private void enableStudyReminders() {
    if (Build.VERSION.SDK_INT < Build.VERSION_CODES.TIRAMISU) {
        StudyNotifications.showStudyReminder(this);
        return;
    }

    if (ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.POST_NOTIFICATIONS
    ) == PackageManager.PERMISSION_GRANTED) {
        StudyNotifications.showStudyReminder(this);
    } else {
        notificationPermission.launch(Manifest.permission.POST_NOTIFICATIONS);
    }
}
```

Register the launcher each time the activity is created, as with other Activity Result launchers. If permission is denied, respect the choice and keep other app features available. Foreground services still need their required notification even though they have a separate exemption from requesting `POST_NOTIFICATIONS` to start.

## Build and post a notification

AndroidX `NotificationCompat` builds notifications that work across Android versions. Add the method below to the `StudyNotifications` helper from the channel example.

Add the following imports to `StudyNotifications.java`, then add this method inside the `StudyNotifications` class from the channel example:

```java
import android.app.PendingIntent;
import android.content.Intent;

import androidx.core.app.NotificationCompat;
import androidx.core.app.NotificationManagerCompat;
```

```java
public static void showStudyReminder(Context context) {
    Intent openApp = new Intent(context, MainActivity.class);
    PendingIntent contentIntent = PendingIntent.getActivity(
            context,
            0,
            openApp,
            PendingIntent.FLAG_UPDATE_CURRENT | PendingIntent.FLAG_IMMUTABLE
    );

    NotificationCompat.Builder notification =
            new NotificationCompat.Builder(context, CHANNEL_ID)
                    .setSmallIcon(android.R.drawable.ic_dialog_info)
                    .setContentTitle("Study reminder")
                    .setContentText("Review the notes you saved today.")
                    .setPriority(NotificationCompat.PRIORITY_DEFAULT)
                    .setContentIntent(contentIntent)
                    .setAutoCancel(true);

    NotificationManagerCompat.from(context).notify(1001, notification.build());
}
```

The example uses a built-in icon so it can stand on its own. A finished app should replace it with an app-owned monochrome status-bar drawable. Call `createChannel()` before `showStudyReminder()`, and only post after the permission check on Android 13 and later. The channel ID in the builder must exactly match the ID used to create the channel.

## Notes to remember

- WorkManager schedules deferrable work that should survive app restarts and can retry.
- Constraints let work wait for conditions such as a network connection or charging.
- Unique work prevents duplicate jobs with the same purpose.
- WorkManager timing is approximate. Periodic work cannot repeat more often than every 15 minutes.
- Use a foreground service only for ongoing work that the user notices and expects to control.
- Android 8.0 and later require notification channels.
- Android 13 and later require runtime notification permission for ordinary notifications.
- Keep the user informed and handle denied permission without blocking unrelated features.

## Practice: schedule a notes sync

Add a worker that sends pending note changes to the API from Chapter 11. Require a connected network, enqueue it as unique work, and return retry when a temporary request failure occurs. Add a study reminder channel, request notification permission when the user enables reminders, and tap the notification to reopen the app. Test the flow with permission granted and denied.

## References

- [Getting started with WorkManager](https://developer.android.com/develop/background-work/background-tasks/persistent/getting-started)
- [Define work requests](https://developer.android.com/develop/background-work/background-tasks/persistent/getting-started/define-work)
- [Manage unique work](https://developer.android.com/develop/background-work/background-tasks/persistent/how-to/manage-work)
- [Foreground services overview](https://developer.android.com/develop/background-work/services/fgs)
- [Foreground service types](https://developer.android.com/develop/background-work/services/fgs/service-types)
- [Create and manage notification channels](https://developer.android.com/develop/ui/views/notifications/channels)
- [Notification runtime permission](https://developer.android.com/develop/ui/compose/notifications/notification-permission)

