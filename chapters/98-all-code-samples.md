# 16. All code samples

[Back to notes index](../README.md)

This chapter gathers the fenced examples from the topic notes in one place. Each example keeps its original language tag and is grouped by the chapter and section where I first recorded it. Read the linked section when you need its explanation or surrounding setup.

The examples cover Java, XML, Gradle, JSON, and the command-line checks used across these notes. Some snippets are focused excerpts and rely on the resources, IDs, dependencies, or classes described in their source section.

## Build your first Android app

[Source chapter](./01-first-android-app.md)

### Example 1: Find the first-screen files

```text
app/
└── src/main/
    ├── AndroidManifest.xml
    ├── java/com/example/studyapp/MainActivity.java
    └── res/
        ├── layout/activity_main.xml
        └── values/strings.xml
```

### Example 2: Build a small screen in XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/message"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/welcome_message"
        android:textSize="20sp" />

    <Button
        android:id="@+id/change_message"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="@string/change_message" />

</LinearLayout>
```

### Example 3: Build a small screen in XML

```xml
<resources>
    <string name="app_name">StudyApp</string>
    <string name="welcome_message">Hello, Android!</string>
    <string name="change_message">Change message</string>
    <string name="updated_message">The button works.</string>
</resources>
```

### Example 4: Connect the layout to Java

```java
package com.example.studyapp;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        TextView message = (TextView) findViewById(R.id.message);
        Button changeMessage = (Button) findViewById(R.id.change_message);

        changeMessage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                message.setText(R.string.updated_message);
            }
        });
    }
}
```

### Example 5: Check the launcher activity

```xml
<activity
    android:name=".MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

## Java essentials for Android

[Source chapter](./02-java-for-android.md)

### Example 1: Values and types

```java
int completedCount = 3;
double progress = 0.75;
boolean isReady = true;
char firstLetter = 'A';
String topic = "Activities";
```

### Example 2: Values and types

```java
final int maximumAttempts = 3;
```

### Example 3: Conditions, loops, and methods

```java
static String statusLabel(int completed, int total) {
    if (total <= 0) {
        return "No topics yet";
    }

    if (completed == total) {
        return "All topics reviewed";
    }

    return completed + " of " + total + " reviewed";
}
```

### Example 4: Conditions, loops, and methods

```java
for (String topic : topics) {
    System.out.println(topic);
}
```

### Example 5: Classes, objects, and encapsulation

```java
public class StudyItem {
    private final String title;
    private boolean reviewed;

    public StudyItem(String title) {
        if (title == null || title.trim().isEmpty()) {
            throw new IllegalArgumentException("Title must not be empty");
        }

        this.title = title.trim();
        this.reviewed = false;
    }

    public String getTitle() {
        return title;
    }

    public boolean isReviewed() {
        return reviewed;
    }

    public void markReviewed() {
        reviewed = true;
    }
}
```

### Example 6: Interfaces, inheritance, and callbacks

```java
public class MainActivity extends Activity {
    // Activity behavior belongs here.
}
```

### Example 7: Interfaces, inheritance, and callbacks

```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        message.setText(R.string.updated_message);
    }
});
```

### Example 8: Lists, generics, and equality

```java
List<StudyItem> items = new ArrayList<>();
items.add(new StudyItem("Java basics"));
items.add(new StudyItem("Activities"));

for (StudyItem item : items) {
    System.out.println(item.getTitle());
}
```

### Example 9: Lists, generics, and equality

```java
String selectedTab = "notes";

if ("notes".equals(selectedTab)) {
    System.out.println("Show the notes tab");
}
```

### Example 10: Null values and exceptions

```java
String countText = "12";

try {
    int count = Integer.parseInt(countText);
    System.out.println("Count: " + count);
} catch (NumberFormatException exception) {
    System.out.println("Enter a whole number");
}
```

## Project structure, Gradle, and resources

[Source chapter](./03-project-structure-and-resources.md)

### Example 1: How an Android project is arranged

```text
StudyApp/
├── gradle/
│   └── wrapper/
│       └── gradle-wrapper.properties
├── app/
│   ├── build.gradle
│   └── src/
│       ├── main/
│       │   ├── AndroidManifest.xml
│       │   ├── java/com/example/studyapp/
│       │   └── res/
│       ├── test/
│       └── androidTest/
├── build.gradle
├── gradlew
├── gradlew.bat
└── settings.gradle
```

### Example 2: Gradle and the Android build plugin

```text
gradlew.bat :app:tasks
```

### Example 3: Gradle and the Android build plugin

```text
gradlew.bat :app:assembleDebug
```

### Example 4: Use a string resource

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">StudyApp</string>
    <string name="welcome_message">Welcome to my study notes</string>
</resources>
```

### Example 5: Use a string resource

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/welcome_message" />
```

### Example 6: Use a string resource

```java
titleView.setText(R.string.welcome_message);
```

## Activities and the Android lifecycle

[Source chapter](./04-activities-and-lifecycle.md)

### Example 1: Common transitions

```text
onCreate -> onStart -> onResume
```

### Example 2: Common transitions

```text
onPause -> onStop
```

### Example 3: Common transitions

```text
onRestart -> onStart -> onResume
```

### Example 4: Observe callbacks with Logcat

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

### Example 5: Restore a small piece of screen state

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

### Example 6: Restore a small piece of screen state

```xml
<string name="tap_count">Taps: %1$d</string>
```

## XML layouts, views, themes, and View Binding

[Source chapter](./05-xml-layouts-and-viewbinding.md)

### Example 1: Layouts describe a view hierarchy

```text
LinearLayout
├── TextView
├── EditText
└── Button
```

### Example 2: A small XML screen

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/screen_title"
        style="@style/StudyApp.Heading"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/screen_title" />

    <EditText
        android:id="@+id/topic_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:hint="@string/topic_hint"
        android:inputType="textCapSentences" />

    <Button
        android:id="@+id/save_topic"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="@string/save_topic" />

</LinearLayout>
```

### Example 3: A small XML screen

```xml
<resources>
    <string name="screen_title">Topics I am studying</string>
    <string name="topic_hint">Enter a topic</string>
    <string name="save_topic">Save topic</string>
</resources>
```

### Example 4: Styles and themes

```xml
<resources>
    <style name="StudyApp.Heading">
        <item name="android:textSize">22sp</item>
        <item name="android:textStyle">bold</item>
    </style>
</resources>
```

### Example 5: View Binding

```groovy
android {
    buildFeatures {
        viewBinding true
    }
}
```

### Example 6: View Binding

```java
package com.example.studyapp;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;

import com.example.studyapp.databinding.ActivityMainBinding;

public class MainActivity extends Activity {
    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        binding.saveTopic.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String topic = binding.topicInput.getText().toString().trim();
                if (!topic.isEmpty()) {
                    binding.screenTitle.setText(topic);
                }
            }
        });
    }
}
```

## Events, forms, and validation

[Source chapter](./06-events-forms-and-validation.md)

### Example 1: Events connect an action to behavior

```java
binding.saveButton.setOnClickListener(view -> saveEntry());
```

### Example 2: Events connect an action to behavior

```java
binding.saveButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        saveEntry();
    }
});
```

### Example 3: Tell the keyboard what the field accepts

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

### Example 4: A small study entry form

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

### Example 5: A small study entry form

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

### Example 6: Read and validate the values in Java

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

### Example 7: Decide when validation should run

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

## Intents, fragments, and navigation

[Source chapter](./07-navigation-intents-and-fragments.md)

### Example 1: An Intent describes an action

```java
import android.content.Intent;

Intent intent = new Intent(this, NoteDetailActivity.class);
intent.putExtra(NoteDetailActivity.EXTRA_NOTE_ID, noteId);
startActivity(intent);
```

### Example 2: An Intent describes an action

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

### Example 3: An Intent describes an action

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

### Example 4: An Intent describes an action

```xml
<activity xmlns:android="http://schemas.android.com/apk/res/android"
    android:name=".NoteDetailActivity"
    android:exported="false" />
```

### Example 5: Implicit intents use another app

```java
import android.content.Intent;

Intent shareIntent = new Intent(Intent.ACTION_SEND);
shareIntent.setType("text/plain");
shareIntent.putExtra(Intent.EXTRA_TEXT, "I am reviewing Android intents.");

Intent chooser = Intent.createChooser(shareIntent, getString(R.string.share_note));
startActivity(chooser);
```

### Example 6: Get an activity result

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

### Example 7: Get an activity result

```java
binding.selectImageButton.setOnClickListener(view -> selectImage.launch("image/*"));
```

### Example 8: An Activity hosts a screen, a Fragment is a reusable part

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

### Example 9: An Activity hosts a screen, a Fragment is a reusable part

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/fragment_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### Example 10: An Activity hosts a screen, a Fragment is a reusable part

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

### Example 11: An Activity hosts a screen, a Fragment is a reusable part

```xml
<resources>
    <string name="share_note">Share note</string>
    <string name="open_note">Open a saved note</string>
    <string name="note_detail_placeholder">Selected note details appear here</string>
</resources>
```

### Example 12: An Activity hosts a screen, a Fragment is a reusable part

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

### Example 13: An Activity hosts a screen, a Fragment is a reusable part

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

### Example 14: An Activity hosts a screen, a Fragment is a reusable part

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

### Example 15: Pass information without coupling screens

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

### Example 16: Pass information without coupling screens

```java
// In the fragment that returns a selection.
Bundle result = new Bundle();
result.putString("topic", selectedTopic);
getParentFragmentManager().setFragmentResult("selected_topic", result);
```

## Lists with RecyclerView and adapters

[Source chapter](./08-recyclerview-and-adapters.md)

### Example 1: Model the rows as data

```java
public final class StudyNote {
    private final long id;
    private final String title;
    private final String summary;

    public StudyNote(long id, String title, String summary) {
        this.id = id;
        this.title = title;
        this.summary = summary;
    }

    public long getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getSummary() {
        return summary;
    }
}
```

### Example 2: Define the screen and one row

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/notes_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:padding="16dp" />

    <TextView
        android:id="@+id/empty_message"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:gravity="center"
        android:padding="24dp"
        android:text="@string/no_study_notes"
        android:visibility="gone" />

</FrameLayout>
```

### Example 3: Define the screen and one row

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:id="@+id/note_title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="18sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/note_summary"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp" />

</LinearLayout>
```

### Example 4: Define the screen and one row

```xml
<resources>
    <string name="no_study_notes">No study notes yet.</string>
</resources>
```

### Example 5: Bind each StudyNote in an adapter

```java
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.DiffUtil;
import androidx.recyclerview.widget.ListAdapter;
import androidx.recyclerview.widget.RecyclerView;

public class StudyNoteAdapter
        extends ListAdapter<StudyNote, StudyNoteAdapter.NoteViewHolder> {

    public interface OnNoteClickListener {
        void onNoteClick(StudyNote note);
    }

    private static final DiffUtil.ItemCallback<StudyNote> DIFF_CALLBACK =
            new DiffUtil.ItemCallback<StudyNote>() {
                @Override
                public boolean areItemsTheSame(
                        @NonNull StudyNote oldItem,
                        @NonNull StudyNote newItem
                ) {
                    return oldItem.getId() == newItem.getId();
                }

                @Override
                public boolean areContentsTheSame(
                        @NonNull StudyNote oldItem,
                        @NonNull StudyNote newItem
                ) {
                    return oldItem.getTitle().equals(newItem.getTitle())
                            && oldItem.getSummary().equals(newItem.getSummary());
                }
            };

    private final OnNoteClickListener clickListener;

    public StudyNoteAdapter(OnNoteClickListener clickListener) {
        super(DIFF_CALLBACK);
        this.clickListener = clickListener;
    }

    @NonNull
    @Override
    public NoteViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View row = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_study_note, parent, false);
        return new NoteViewHolder(row);
    }

    @Override
    public void onBindViewHolder(@NonNull NoteViewHolder holder, int position) {
        StudyNote note = getItem(position);
        holder.title.setText(note.getTitle());
        holder.summary.setText(note.getSummary());

        holder.itemView.setOnClickListener(view -> {
            int currentPosition = holder.getBindingAdapterPosition();
            if (currentPosition != RecyclerView.NO_POSITION) {
                clickListener.onNoteClick(getItem(currentPosition));
            }
        });
    }

    static class NoteViewHolder extends RecyclerView.ViewHolder {
        private final TextView title;
        private final TextView summary;

        NoteViewHolder(@NonNull View itemView) {
            super(itemView);
            title = itemView.findViewById(R.id.note_title);
            summary = itemView.findViewById(R.id.note_summary);
        }
    }
}
```

### Example 6: Attach the adapter and provide sample data

```java
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.TextView;

import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;
import java.util.List;

public class NotesActivity extends Activity {
    private StudyNoteAdapter adapter;
    private TextView emptyMessage;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_notes);

        RecyclerView notesList = findViewById(R.id.notes_list);
        emptyMessage = findViewById(R.id.empty_message);

        adapter = new StudyNoteAdapter(note -> {
            Intent intent = new Intent(this, NoteDetailActivity.class);
            intent.putExtra(NoteDetailActivity.EXTRA_NOTE_ID, note.getId());
            startActivity(intent);
        });

        notesList.setLayoutManager(new LinearLayoutManager(this));
        notesList.setAdapter(adapter);
        showNotes(createSampleNotes());
    }

    private List<StudyNote> createSampleNotes() {
        List<StudyNote> notes = new ArrayList<>();
        notes.add(new StudyNote(1L, "Activity lifecycle", "Callbacks and saved state"));
        notes.add(new StudyNote(2L, "RecyclerView", "Adapters and view holders"));
        notes.add(new StudyNote(3L, "Intents", "Move between app screens"));
        return notes;
    }

    private void showNotes(List<StudyNote> notes) {
        adapter.submitList(notes);
        emptyMessage.setVisibility(notes.isEmpty() ? View.VISIBLE : View.GONE);
    }
}
```

### Example 7: Attach the adapter and provide sample data

```java
List<StudyNote> updatedNotes = new ArrayList<>(adapter.getCurrentList());
updatedNotes.add(new StudyNote(4L, "Activities", "Lifecycle and state"));
adapter.submitList(updatedNotes);
```

### Example 8: Choose a layout manager

```java
notesList.setLayoutManager(new LinearLayoutManager(this));
```

## App architecture with ViewModel and LiveData

[Source chapter](./09-architecture-viewmodel-livedata.md)

### Example 1: Let state flow to the screen

```text
User action -> Activity method -> ViewModel state changes
                                      |
                                      v
Activity observes LiveData -> renders the current state
```

### Example 2: Create a ViewModel that exposes read-only state

```java
import androidx.lifecycle.LiveData;
import androidx.lifecycle.MutableLiveData;
import androidx.lifecycle.ViewModel;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class NotesViewModel extends ViewModel {
    private final MutableLiveData<List<StudyNote>> notes =
            new MutableLiveData<>(Collections.emptyList());
    private long nextId = 1L;

    public LiveData<List<StudyNote>> getNotes() {
        return notes;
    }

    public void addNote(String title) {
        List<StudyNote> updated = new ArrayList<>(notes.getValue());
        updated.add(new StudyNote(
                nextId++,
                title,
                "Added during this session"
        ));
        notes.setValue(Collections.unmodifiableList(updated));
    }
}
```

### Example 3: Get the ViewModel and observe its state

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/title_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/note_title_hint"
        android:inputType="textCapSentences" />

    <Button
        android:id="@+id/add_note"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/add_note" />

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/notes_list"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

        <TextView
            android:id="@+id/empty_message"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:gravity="center"
            android:text="@string/no_study_notes"
            android:visibility="gone" />

    </FrameLayout>

</LinearLayout>
```

### Example 4: Get the ViewModel and observe its state

```xml
<resources>
    <string name="no_study_notes">No study notes yet.</string>
    <string name="note_title_hint">Enter a study topic</string>
    <string name="add_note">Add note</string>
</resources>
```

### Example 5: Get the ViewModel and observe its state

```java
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import androidx.lifecycle.ViewModelProvider;
import androidx.fragment.app.FragmentActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

public class NotesActivity extends FragmentActivity {
    private NotesViewModel viewModel;
    private StudyNoteAdapter adapter;
    private EditText titleInput;
    private TextView emptyMessage;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_notes);

        RecyclerView notesList = findViewById(R.id.notes_list);
        titleInput = findViewById(R.id.title_input);
        emptyMessage = findViewById(R.id.empty_message);
        Button addButton = findViewById(R.id.add_note);

        adapter = new StudyNoteAdapter(note -> {
            // Open the selected note using its ID.
        });
        notesList.setLayoutManager(new LinearLayoutManager(this));
        notesList.setAdapter(adapter);

        viewModel = new ViewModelProvider(this).get(NotesViewModel.class);
        viewModel.getNotes().observe(this, notes -> {
            adapter.submitList(notes);
            emptyMessage.setVisibility(notes.isEmpty() ? View.VISIBLE : View.GONE);
        });

        addButton.setOnClickListener(view -> {
            String title = titleInput.getText().toString().trim();
            if (!title.isEmpty()) {
                viewModel.addNote(title);
                titleInput.setText("");
            }
        });
    }
}
```

### Example 6: Observe from a Fragment's view lifecycle

```java
viewModel.getNotes().observe(getViewLifecycleOwner(), notes -> {
    adapter.submitList(notes);
    emptyMessage.setVisibility(notes.isEmpty() ? View.VISIBLE : View.GONE);
});
```

### Example 7: Keep work in the appropriate layer

```text
Activity or Fragment <-> ViewModel <-> Repository <-> Room or network
       UI events          screen state          app data
```

## Local data with Room and preferences

[Source chapter](./10-local-data-room-and-preferences.md)

### Example 1: Keep the Java version in mind

```groovy
dependencies {
    def roomVersion = "2.8.4"

    implementation "androidx.room:room-runtime:$roomVersion"
    annotationProcessor "androidx.room:room-compiler:$roomVersion"
    implementation "androidx.room:room-livedata:$roomVersion"
}
```

### Example 2: Define a study note entity

```java
import androidx.annotation.NonNull;
import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity(tableName = "study_notes")
public class StudyNoteEntity {
    @PrimaryKey(autoGenerate = true)
    public long id;

    @NonNull
    public String title;

    public String body;

    public long createdAt;
}
```

### Example 3: Put database operations in a DAO

```java
import androidx.lifecycle.LiveData;
import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.Query;

import java.util.List;

@Dao
public interface StudyNoteDao {
    @Query("SELECT * FROM study_notes ORDER BY createdAt DESC")
    LiveData<List<StudyNoteEntity>> observeAll();

    @Insert
    void insert(StudyNoteEntity note);

    @Query("DELETE FROM study_notes WHERE id = :noteId")
    void deleteById(long noteId);
}
```

### Example 4: Create the database once

```java
import androidx.room.Database;
import androidx.room.RoomDatabase;

@Database(entities = {StudyNoteEntity.class}, version = 1, exportSchema = true)
public abstract class StudyDatabase extends RoomDatabase {
    public abstract StudyNoteDao studyNoteDao();
}
```

### Example 5: Create the database once

```java
import android.content.Context;

import androidx.room.Room;

public final class StudyDatabaseProvider {
    private static volatile StudyDatabase instance;

    private StudyDatabaseProvider() {
    }

    public static StudyDatabase get(Context context) {
        if (instance == null) {
            synchronized (StudyDatabaseProvider.class) {
                if (instance == null) {
                    instance = Room.databaseBuilder(
                            context.getApplicationContext(),
                            StudyDatabase.class,
                            "study-notes.db"
                    ).build();
                }
            }
        }
        return instance;
    }
}
```

### Example 6: Keep the DAO behind a repository

```java
import android.content.Context;

import androidx.lifecycle.LiveData;

import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class StudyNoteRepository {
    private final StudyNoteDao noteDao;
    private static final ExecutorService DATABASE_EXECUTOR =
            Executors.newSingleThreadExecutor();

    public StudyNoteRepository(Context context) {
        noteDao = StudyDatabaseProvider.get(context).studyNoteDao();
    }

    public LiveData<List<StudyNoteEntity>> observeNotes() {
        return noteDao.observeAll();
    }

    public void save(StudyNoteEntity note) {
        DATABASE_EXECUTOR.execute(() -> noteDao.insert(note));
    }

    public void delete(long noteId) {
        DATABASE_EXECUTOR.execute(() -> noteDao.deleteById(noteId));
    }
}
```

### Example 7: Keep the DAO behind a repository

```java
viewModel.getNotes().observe(this, notes -> {
    adapter.submitList(notes);
    emptyMessage.setVisibility(notes.isEmpty() ? View.VISIBLE : View.GONE);
});
```

### Example 8: Update schemas with migrations

```java
import androidx.room.migration.Migration;
import androidx.sqlite.db.SupportSQLiteDatabase;

static final Migration MIGRATION_1_2 = new Migration(1, 2) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        database.execSQL(
                "ALTER TABLE study_notes "
                        + "ADD COLUMN pinned INTEGER NOT NULL DEFAULT 0"
        );
    }
};
```

### Example 9: Update schemas with migrations

```java
Room.databaseBuilder(context, StudyDatabase.class, "study-notes.db")
        .addMigrations(MIGRATION_1_2)
        .build();
```

### Example 10: Save a small setting with SharedPreferences

```java
import android.content.Context;
import android.content.SharedPreferences;

public final class StudySettings {
    private static final String FILE_NAME = "com.example.studyapp.SETTINGS";
    private static final String KEY_COMPACT_LIST = "compact_list";

    private StudySettings() {
    }

    public static boolean isCompactList(Context context) {
        SharedPreferences preferences = context.getSharedPreferences(
                FILE_NAME,
                Context.MODE_PRIVATE
        );
        return preferences.getBoolean(KEY_COMPACT_LIST, false);
    }

    public static void setCompactList(Context context, boolean enabled) {
        SharedPreferences preferences = context.getSharedPreferences(
                FILE_NAME,
                Context.MODE_PRIVATE
        );
        preferences.edit()
                .putBoolean(KEY_COMPACT_LIST, enabled)
                .apply();
    }
}
```

## Networking, REST APIs, and JSON

[Source chapter](./11-networking-and-rest-apis.md)

### Example 1: Allow network access in the manifest

```xml
<uses-permission xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="android.permission.INTERNET" />
```

### Example 2: Read a JSON response with HttpURLConnection

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

### Example 3: Keep the request off the main thread

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

### Example 4: Understand the JSON shape

```json
{
  "id": 1,
  "title": "Activity lifecycle",
  "body": "Callbacks and saved state"
}
```

### Example 5: Understand the JSON shape

```java
JSONArray posts = responseObject.getJSONArray("items");
List<String> titles = new ArrayList<>();

for (int index = 0; index < posts.length(); index++) {
    JSONObject post = posts.getJSONObject(index);
    titles.add(post.optString("title", "Untitled post"));
}
```

### Example 6: Send JSON in a request

```json
{
  "title": "Networking notes",
  "body": "Requests run outside the UI thread."
}
```

## Background work and notifications

[Source chapter](./12-background-work-and-notifications.md)

### Example 1: Define work with WorkManager

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

### Example 2: Define work with WorkManager

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

### Example 3: Define work with WorkManager

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

### Example 4: Create a notification channel

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

### Example 5: Request notification permission when needed

```xml
<uses-permission xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="android.permission.POST_NOTIFICATIONS" />
```

### Example 6: Request notification permission when needed

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

### Example 7: Build and post a notification

```java
import android.app.PendingIntent;
import android.content.Intent;

import androidx.core.app.NotificationCompat;
import androidx.core.app.NotificationManagerCompat;
```

### Example 8: Build and post a notification

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

## Permissions, privacy, and device features

[Source chapter](./13-permissions-and-device-features.md)

### Example 1: Declare an optional camera capability

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature
        android:name="android.hardware.camera.any"
        android:required="false" />

    <application />

</manifest>
```

### Example 2: Declare an optional camera capability

```java
import android.content.pm.PackageManager;

boolean hasCamera = getPackageManager().hasSystemFeature(
        PackageManager.FEATURE_CAMERA_ANY
);

if (hasCamera) {
    showCameraButton();
} else {
    showPhotoSelectionOption();
}
```

### Example 3: Request a runtime permission in context

```java
import android.Manifest;
import android.content.pm.PackageManager;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

private final ActivityResultLauncher<String> cameraPermission =
        registerForActivityResult(
                new ActivityResultContracts.RequestPermission(),
                granted -> {
                    if (granted) {
                        openCameraFeature();
                    } else {
                        showCameraUnavailableMessage();
                    }
                }
        );

private void onCameraButtonTap() {
    if (!getPackageManager().hasSystemFeature(PackageManager.FEATURE_CAMERA_ANY)) {
        showCameraUnavailableMessage();
        return;
    }

    if (ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.CAMERA
    ) == PackageManager.PERMISSION_GRANTED) {
        openCameraFeature();
    } else if (ActivityCompat.shouldShowRequestPermissionRationale(
            this,
            Manifest.permission.CAMERA
    )) {
        showCameraRationale(() -> cameraPermission.launch(Manifest.permission.CAMERA));
    } else {
        cameraPermission.launch(Manifest.permission.CAMERA);
    }
}
```

### Example 4: Prefer system selection when it avoids broad access

```java
import android.net.Uri;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.PickVisualMediaRequest;
import androidx.activity.result.contract.ActivityResultContracts;

private final ActivityResultLauncher<PickVisualMediaRequest> pickImage =
        registerForActivityResult(
                new ActivityResultContracts.PickVisualMedia(),
                (Uri uri) -> {
                    if (uri != null) {
                        previewSelectedImage(uri);
                    }
                }
        );

private void onChooseImageTap() {
    pickImage.launch(new PickVisualMediaRequest.Builder()
            .setMediaType(
                    ActivityResultContracts.PickVisualMedia.ImageOnly.INSTANCE
            )
            .build());
}
```

## Accessibility and adaptive layouts

[Source chapter](./14-accessibility-and-adaptive-ui.md)

### Example 1: 2. Give controls useful spoken names

```xml
<!-- res/layout/activity_search.xml -->
<ImageButton
    android:id="@+id/searchButton"
    android:layout_width="48dp"
    android:layout_height="48dp"
    android:contentDescription="@string/search"
    android:src="@drawable/ic_search"
    android:background="?attr/selectableItemBackgroundBorderless" />
```

### Example 2: 2. Give controls useful spoken names

```xml
<!-- res/values/strings.xml -->
<resources>
    <string name="search">Search</string>
    <string name="search_notes">Search notes</string>
    <string name="email_address">Email address</string>
    <string name="email_hint">name@example.com</string>
    <string name="more_options">More options</string>
    <string name="notes_title">My notes</string>
    <string name="clear_search">Clear search</string>
    <string name="select_a_note">Select a note to read</string>
</resources>
```

### Example 3: 2. Give controls useful spoken names

```xml
<ImageView
    android:layout_width="match_parent"
    android:layout_height="160dp"
    android:importantForAccessibility="no"
    android:src="@drawable/header_texture"
    android:contentDescription="@null" />
```

### Example 4: 2. Give controls useful spoken names

```xml
<TextView
    android:id="@+id/emailLabel"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:labelFor="@id/emailInput"
    android:text="@string/email_address" />

<EditText
    android:id="@+id/emailInput"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:inputType="textEmailAddress"
    android:hint="@string/email_hint" />
```

### Example 5: 3. Make touch targets easy to reach

```xml
<ImageButton
    android:id="@+id/moreButton"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:minWidth="48dp"
    android:minHeight="48dp"
    android:padding="12dp"
    android:contentDescription="@string/more_options"
    android:src="@drawable/ic_more_vert" />
```

### Example 6: 4. Keep reading and focus order clear

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <TextView
        android:id="@+id/pageTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/notes_title" />

    <EditText
        android:id="@+id/searchInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/search_notes" />

    <Button
        android:id="@+id/clearButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/clear_search" />
</LinearLayout>
```

### Example 7: 6. Understand responsive and adaptive layouts

```text
res/
  layout/
    activity_notes.xml
  layout-w600dp/
    activity_notes.xml
```

### Example 8: 6. Understand responsive and adaptive layouts

```xml
<!-- res/layout/activity_notes.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/notesTitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/notes_title"
        android:textSize="24sp" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/notesList"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

### Example 9: 6. Understand responsive and adaptive layouts

```xml
<!-- res/layout-w600dp/activity_notes.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/notesList"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <FrameLayout
        android:id="@+id/noteDetailContainer"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="2">

        <TextView
            android:id="@+id/noteDetailPlaceholder"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="@string/select_a_note" />
    </FrameLayout>
</LinearLayout>
```

### Example 10: 6. Understand responsive and adaptive layouts

```java
FrameLayout detailContainer = findViewById(R.id.noteDetailContainer);

if (detailContainer != null) {
    // A wide layout is active, so show the selected note here.
} else {
    // A narrow layout can open the selected note on a separate screen.
}
```

## Testing, debugging, and release

[Source chapter](./15-debugging-testing-and-release.md)

### Example 1: 2. Write a local Java unit test

```java
// app/src/main/java/com/example/notes/NoteTitleValidator.java
package com.example.notes;

public final class NoteTitleValidator {
    private NoteTitleValidator() {
    }

    public static boolean isValid(String title) {
        return title != null && !title.trim().isEmpty();
    }
}
```

### Example 2: 2. Write a local Java unit test

```java
package com.example.notes;

import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;

import org.junit.Test;

public class NoteTitleValidatorTest {
    @Test
    public void isValid_returnsTrueForText() {
        assertTrue(NoteTitleValidator.isValid("Android notes"));
    }

    @Test
    public void isValid_returnsFalseForBlankText() {
        assertFalse(NoteTitleValidator.isValid("   "));
    }

    @Test
    public void isValid_returnsFalseForNull() {
        assertFalse(NoteTitleValidator.isValid(null));
    }
}
```

### Example 3: 2. Write a local Java unit test

```powershell
.\gradlew.bat test
```

### Example 4: 3. Check a screen with an instrumented UI test

```java
// app/src/androidTest/java/com/example/notes/NoteSaveTest.java
package com.example.notes;

import static androidx.test.espresso.Espresso.onView;
import static androidx.test.espresso.action.ViewActions.click;
import static androidx.test.espresso.assertion.ViewAssertions.matches;
import static androidx.test.espresso.matcher.ViewMatchers.isDisplayed;
import static androidx.test.espresso.matcher.ViewMatchers.withId;

import androidx.test.ext.junit.rules.ActivityScenarioRule;
import androidx.test.ext.junit.runners.AndroidJUnit4;

import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;

@RunWith(AndroidJUnit4.class)
public class NoteSaveTest {
    @Rule
    public ActivityScenarioRule<MainActivity> activityRule =
            new ActivityScenarioRule<>(MainActivity.class);

    @Test
    public void saveButton_showsConfirmation() {
        onView(withId(R.id.saveButton)).perform(click());
        onView(withId(R.id.noteSavedMessage)).check(matches(isDisplayed()));
    }
}
```

### Example 5: 3. Check a screen with an instrumented UI test

```powershell
.\gradlew.bat connectedAndroidTest
```

### Example 6: 4. Use Logcat to understand runtime behavior

```java
import android.util.Log;

public final class NoteRepository {
    private static final String TAG = "NoteRepository";

    public void saveNote(String title) {
        if (title == null || title.trim().isEmpty()) {
            Log.w(TAG, "Skipped saving a note with an empty title");
            return;
        }

        // Save the note.
        Log.d(TAG, "Note saved");
    }
}
```

### Example 7: 6. Check the release build separately

```groovy
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'),
                    'proguard-rules.pro'
        }
    }
}
```

---

[Previous: Testing, debugging, and release](15-debugging-testing-and-release.md) | [Notes index](../README.md) | [Next: Complete Q&A](99-complete-q-and-a.md)
