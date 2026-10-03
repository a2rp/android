# 6. Events, forms, and validation

[Back to notes index](../README.md)

| [Previous: XML layouts, views, themes, and View Binding](05-xml-layouts-and-viewbinding.md) | [Notes index](../README.md) | [Next: Intents, fragments, and navigation](07-navigation-intents-and-fragments.md) |
|:--|:--:|--:|

## Events connect an action to behavior

An event is something that happens in the interface, such as a button tap, a text change, or a selection. A listener receives that event so Java code can respond. For a button action, `setOnClickListener()` is the usual choice:

```java
binding.saveButton.setOnClickListener(view -> saveEntry());
```

The lambda is shorthand for an implementation of `View.OnClickListener`. In a project using an older Java source level, write the listener as an anonymous class:

```java
binding.saveButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        saveEntry();
    }
});
```

Use the highest-level listener that describes the interaction. A click listener represents an action. A touch listener exposes lower-level touch events and is useful for gestures or custom touch behavior, but it should not replace a click listener for an ordinary button. Do not use keyboard key events to detect text typed into an `EditText`; the input method can be a software keyboard and does not behave like a physical key for every character.

## Tell the keyboard what the field accepts

`android:inputType` hints the kind of content a field accepts and helps the system choose an appropriate keyboard. It does not validate the value. A numeric keyboard makes number entry easier, but code must still handle empty or malformed input.

`android:imeOptions` changes the action shown on the keyboard, such as Next or Done. It also does not validate input. Keep a visible button for the main action so it remains discoverable to touch, keyboard, and accessibility users.

```xml
<EditText
    android:id="@+id/topic_input"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="@string/topic_hint"
    android:inputType="textCapSentences"
    android:imeOptions="actionNext" />

<EditText
    android:id="@+id/minutes_input"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="@string/minutes_hint"
    android:inputType="number"
    android:imeOptions="actionDone" />
```

Use `textEmailAddress`, `phone`, `numberDecimal`, or another suitable input type when it matches the data. The right choice improves entry, but never treat the keyboard layout as proof that a value is valid.

## A small study entry form

This screen records a topic and the number of minutes spent on it. Text is kept in string resources, and each field has a visible label. A hint can disappear while someone types, so it should not be the only label for important fields.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/screen_title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/study_entry_title"
        android:textSize="22sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/topic_label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dp"
        android:labelFor="@id/topic_input"
        android:text="@string/topic_label" />

    <EditText
        android:id="@+id/topic_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/topic_hint"
        android:inputType="textCapSentences"
        android:imeOptions="actionNext" />

    <TextView
        android:id="@+id/minutes_label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:labelFor="@id/minutes_input"
        android:text="@string/minutes_label" />

    <EditText
        android:id="@+id/minutes_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/minutes_hint"
        android:inputType="number"
        android:imeOptions="actionDone" />

    <Button
        android:id="@+id/save_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="@string/save_entry" />

    <TextView
        android:id="@+id/save_status"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:accessibilityLiveRegion="polite" />

</LinearLayout>
```

The matching values in `res/values/strings.xml`:

```xml
<resources>
    <string name="study_entry_title">Record a study session</string>
    <string name="topic_label">Topic</string>
    <string name="topic_hint">For example, activity lifecycle</string>
    <string name="minutes_label">Minutes studied</string>
    <string name="minutes_hint">Enter a number from 1 to 600</string>
    <string name="save_entry">Save entry</string>
    <string name="topic_required">Enter a topic.</string>
    <string name="minutes_required">Enter the number of minutes.</string>
    <string name="minutes_invalid">Enter a whole number from 1 to 600.</string>
    <string name="entry_saved">Saved %1$s for %2$d minutes.</string>
</resources>
```

The `android:labelFor` attribute connects each visible label to its field for accessibility services. The status view announces a changed message politely, without interrupting the user.

## Read and validate the values in Java

With View Binding enabled as described in the layout notes, the generated `ActivityMainBinding` exposes the fields by their IDs. The click callback calls one method, which reads the current values, validates them, and only then updates the screen.

```java
package com.example.studyapp;

import android.app.Activity;
import android.os.Bundle;
import android.text.TextUtils;
import android.view.View;

import com.example.studyapp.databinding.ActivityMainBinding;

public class MainActivity extends Activity {
    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        binding.saveButton.setOnClickListener(view -> saveEntry());
    }

    private void saveEntry() {
        String topic = binding.topicInput.getText().toString().trim();
        String minutesText = binding.minutesInput.getText().toString().trim();

        binding.topicInput.setError(null);
        binding.minutesInput.setError(null);
        binding.saveStatus.setText("");

        if (TextUtils.isEmpty(topic)) {
            binding.topicInput.setError(getString(R.string.topic_required));
            binding.topicInput.requestFocus();
            return;
        }

        if (TextUtils.isEmpty(minutesText)) {
            binding.minutesInput.setError(getString(R.string.minutes_required));
            binding.minutesInput.requestFocus();
            return;
        }

        final int minutes;
        try {
            minutes = Integer.parseInt(minutesText);
        } catch (NumberFormatException exception) {
            binding.minutesInput.setError(getString(R.string.minutes_invalid));
            binding.minutesInput.requestFocus();
            return;
        }

        if (minutes < 1 || minutes > 600) {
            binding.minutesInput.setError(getString(R.string.minutes_invalid));
            binding.minutesInput.requestFocus();
            return;
        }

        binding.saveStatus.setText(
                getString(R.string.entry_saved, topic, minutes)
        );
    }
}
```

`trim()` removes leading and trailing whitespace, so a field containing only spaces counts as empty. `setError()` associates a message with the field, and `requestFocus()` moves focus to the value that needs attention. Each invalid case returns before the success state, so invalid data is never treated as saved.

The `try` and `catch` around `Integer.parseInt()` matters even with a numeric keyboard. Text can arrive through paste, an accessibility service, or another input path. Parsing can fail, and handling `NumberFormatException` keeps the activity from crashing. A valid number can still be outside the allowed range, so the range check is separate.

For form code that uses `findViewById()` rather than binding, the same rules apply. Read the field with `editText.getText().toString().trim()`, validate it, and only continue after it passes.

## Decide when validation should run

For a short form, validating after the user presses Save is often clear and predictable. It avoids showing an error while someone is still entering a value. After an invalid submission, show a short field-specific message and keep the entered values so they can correct the problem.

`TextWatcher` observes changes to editable text. It can support immediate feedback, such as a character count, or enable a button when a field is nonempty. It runs as the text changes, so keep its work small. Avoid network requests or expensive processing on every keystroke. If live validation is needed, wait until the user has paused typing or validate after a field loses focus. Do not attach the same watcher repeatedly, since that can result in duplicate callbacks.

```java
binding.topicInput.addTextChangedListener(new android.text.TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence text, int start, int count, int after) {
        // No work needed before the text changes.
    }

    @Override
    public void onTextChanged(CharSequence text, int start, int before, int count) {
        binding.saveButton.setEnabled(text.toString().trim().length() > 0);
    }

    @Override
    public void afterTextChanged(android.text.Editable editable) {
        // Keep this empty when the check belongs in onTextChanged.
    }
});
```

If code changes the same field from inside a watcher, it can trigger another text change. Avoid that loop or remove the watcher while applying programmatic formatting.

## Event and form checks

- Use a click listener for a normal button action. Use touch events only when the interaction needs gesture-level details.
- Set `inputType` to help the user enter the expected kind of value, then validate in Java.
- Treat empty strings, whitespace, parsing failures, and range limits as separate checks.
- Use concise error text that says how to fix the field.
- Keep the entered values when showing an error, and do not report success until every required value passes.
- Move focus to the first invalid field and make sure the message is available to accessibility services.
- Keep click callbacks short. Move slow work away from the main thread and show a clear pending state if saving later involves a network request.
- Prevent repeated submission while an asynchronous save is already running, then restore the button when that operation finishes.

## Practice: add a study entry

Use the screen and `saveEntry()` example to record a topic and time spent. Try an empty topic, whitespace in the topic, a blank duration, text pasted into the number field, zero, a value above 600, and a valid entry. Confirm that invalid input shows a useful message, keeps the form values, and never displays the saved status. Then enable and test the keyboard Next and Done actions on an emulator or device.

## Notes to remember

- A listener connects a user action to Java behavior.
- `inputType` helps configure the keyboard but does not validate a value.
- Read editable text as a `String`, trim it, and check it before using it.
- Parsing and range checks solve different problems. Handle both.
- Show errors beside the field that needs correction and stop before success behavior.
- Use `TextWatcher` for lightweight feedback when it improves the interaction.

## References

- [Handle input events](https://developer.android.com/develop/ui/views/touch-and-input/input-events)
- [EditText reference](https://developer.android.com/reference/android/widget/EditText)
- [Specify input method types](https://developer.android.com/develop/ui/views/touch-and-input/keyboard-input/style)
- [Handle keyboard input](https://developer.android.com/develop/ui/views/touch-and-input/keyboard-input)

