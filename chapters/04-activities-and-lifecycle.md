# 4. Activities and the Android lifecycle

[Back to notes index](../README.md)

| [Previous: Project structure, Gradle, and resources](03-project-structure-and-resources.md) | [Notes index](../README.md) | [Next: XML layouts, views, themes, and View Binding](05-xml-layouts-and-viewbinding.md) |
|:--|:--:|--:|

## Activity as a screen controller

An `Activity` is an Android app component that provides a window for a user interface. The system creates and manages activity instances as the user opens, leaves, and returns to screens. An activity is declared in the manifest, and its lifecycle callbacks are the points where the app can respond to those changes.

For a Java and XML screen, `onCreate()` is where I usually inflate the layout, find views, restore small saved state, and connect listeners. The activity should coordinate the screen. Data that needs to outlive an activity instance belongs in a suitable state holder or storage layer, covered in later notes.

## The main lifecycle callbacks

| Callback | What it tells me | Typical work |
| --- | --- | --- |
| `onCreate()` | A new activity instance is being created. | Set up the screen, initialize fields, and restore saved state if supplied. |
| `onStart()` | The activity is becoming visible. | Start work needed while the activity can be seen. |
| `onResume()` | The activity is in the foreground and ready for interaction. | Resume focused interactions such as a camera preview or an animation that should run only while active. |
| `onPause()` | The activity is losing focus. It may still be partly visible. | Pause work that should not continue while the user is interacting elsewhere. Keep this callback quick. |
| `onStop()` | The activity is no longer visible. | Release or reduce resources that are not needed while the screen is hidden. |
| `onDestroy()` | This activity instance is being removed. | Release remaining instance-owned resources when this callback occurs. Do not depend on it for saving important data. |

`onRestart()` can occur when a stopped activity is coming back. It is followed by `onStart()` and `onResume()`. It is not one of the six main callbacks listed above, but it can be useful when reading lifecycle logs.

## Common transitions

The usual first launch sequence is:

```text
onCreate -> onStart -> onResume
```

When another screen or app fully covers the activity, it usually receives:

```text
onPause -> onStop
```

When the user returns to that stopped activity, it usually receives:

```text
onRestart -> onStart -> onResume
```

An interruption that only takes focus may call `onPause()` without `onStop()`. For example, multi-window or a temporary overlay can leave an activity visible but not in focus. The exact transitions depend on what is covering the activity and the device state, so lifecycle code should respond to the callback meaning rather than assume every transition is identical.

When an activity finishes, it normally moves through the pause and stop states before destruction. A configuration change such as rotation can destroy the current instance and create a new one. The system may also terminate a stopped app process to reclaim memory. In that case, the app cannot rely on `onDestroy()` being called before the process disappears.

## Observe callbacks with Logcat

Logging each callback makes transitions easier to understand. Add the following pattern to an activity and replace the message for each callback you want to observe:

```java
private static final String TAG = "MainActivity";

@Override
protected void onStart() {
    super.onStart();
    Log.d(TAG, "onStart");
}

@Override
protected void onStop() {
    super.onStop();
    Log.d(TAG, "onStop");
}
```

Import `android.util.Log`, run the app, and watch Logcat while opening another app, returning, rotating the device, and finishing the activity. Each app action can trigger more than one callback, so read the log in order.

## Restore a small piece of screen state

The `Bundle` passed to `onCreate()` can contain state saved when Android recreates the activity. This is appropriate for small transient values needed to reconstruct the visible screen. It is not long-term storage for app data.

```java
package com.example.studyapp;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends Activity {
    private static final String STATE_TAP_COUNT = "tap_count";

    private int tapCount;
    private TextView countLabel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        countLabel = (TextView) findViewById(R.id.count_label);
        Button countButton = (Button) findViewById(R.id.count_button);

        if (savedInstanceState != null) {
            tapCount = savedInstanceState.getInt(STATE_TAP_COUNT, 0);
        }
        updateCountLabel();

        countButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                tapCount++;
                updateCountLabel();
            }
        });
    }

    private void updateCountLabel() {
        countLabel.setText(getString(R.string.tap_count, tapCount));
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        outState.putInt(STATE_TAP_COUNT, tapCount);
        super.onSaveInstanceState(outState);
    }
}
```

The corresponding views need the IDs `count_label` and `count_button`. Add the formatted string to `res/values/strings.xml`:

```xml
<string name="tap_count">Taps: %1$d</string>
```

`getString()` formats `%1$d` with the integer count. `onSaveInstanceState()` writes a small integer into the outgoing bundle. When Android recreates the activity with saved state, `onCreate()` reads it and redraws the label. View IDs also let Android restore standard view state, such as text entered into an identified `EditText`.

## Choose storage by how long the data should live

| Data example | Suitable place |
| --- | --- |
| A view reference or temporary animation handle | The current activity instance. Recreate it in `onCreate()`. |
| Screen data needed during rotation | A `ViewModel`, which is introduced in the architecture notes. |
| A small value needed if Android recreates the activity after process death | Saved instance state, such as `onSaveInstanceState()`. |
| User data that should remain after closing or restarting the app | Persistent storage, covered in the local data notes. |

Saved instance state should stay small. Store a value or an identifier that lets the screen reload data. Do not put large objects, bitmaps, or a whole database result into the bundle.

## Lifecycle mistakes I watch for

- Saving important data only in `onDestroy()`. Android may end the process without calling it.
- Doing slow work in `onPause()`. A new activity waits for this callback to finish.
- Assuming `onPause()` always means the activity is fully hidden. `onStop()` marks that it is no longer visible.
- Keeping a reference to an old activity or view after configuration recreation.
- Treating rotation as a process restart. The activity can be recreated while the process remains alive.
- Saving large objects in a `Bundle` instead of saving a small key and loading the data from its source.

## Practice: check activity recreation

Use the tap counter above and follow this sequence:

1. Run the screen and tap the button several times.
2. Rotate the emulator and confirm that the counter value remains.
3. Press Home, then return to the app and observe the lifecycle log.
4. Close the activity with Back and open it again. Note whether the screen starts with a new count.

The rotation case tests saved transient state. The close-and-reopen case is different because finishing the activity ends that screen session. Long-term user data needs persistent storage instead.

## Notes to remember

- The activity lifecycle describes the state of one activity instance.
- The activity is visible between `onStart()` and `onStop()`, and ready for direct interaction between `onResume()` and `onPause()`.
- A configuration change can replace an activity instance.
- `onSaveInstanceState()` is for a small amount of transient UI state, not durable app data.
- Do short work in lifecycle callbacks and release resources at the point they are no longer needed.

## References

- [Introduction to activities](https://developer.android.com/guide/components/activities/intro-activities)
- [The activity lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)
- [Save UI states for Views](https://developer.android.com/topic/libraries/architecture/views/saving-states-views)


