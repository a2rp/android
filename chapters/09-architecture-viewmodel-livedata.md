# 9. App architecture with ViewModel and LiveData

[Back to notes index](../README.md)

| [Previous: Lists with RecyclerView and adapters](08-recyclerview-and-adapters.md) | [Notes index](../README.md) | [Next: Local data with Room and preferences](10-local-data-room-and-preferences.md) |
|:--|:--:|--:|

## Keep screen state outside the Activity

An activity or fragment is recreated during configuration changes such as a screen rotation. A field stored only in that activity instance is lost when the old instance is destroyed. A `ViewModel` holds screen-level state for as long as its `ViewModelStoreOwner` remains active, so a replacement activity can reconnect to the same state after rotation.

| Where state lives | What it normally survives |
| --- | --- |
| Activity or fragment field | Only the current instance. |
| `ViewModel` | Configuration changes while its owner remains active. |
| Saved state | A small amount of UI state after system recreation. |
| Room or another persistent store | Activity recreation, process death, and later app launches. |

A `ViewModel` is kept in memory. It does not replace a database and it does not survive process death by itself. For the difference between screen state and saved app data, I keep the important values in the right layer instead of putting everything into the ViewModel.

The AndroidX Lifecycle libraries provide `ViewModel`, `LiveData`, `MutableLiveData`, and `ViewModelProvider`. The examples below use the Java APIs and the `StudyNote` model and `StudyNoteAdapter` from the previous chapter.

The app module needs the AndroidX Lifecycle ViewModel and LiveData artifacts, following the versions and dependency conventions already used by the project.

## Let state flow to the screen

The screen sends user actions to its ViewModel. The ViewModel changes its state, and the screen observes that state and renders it. This keeps display code in the activity and state changes in one screen-level object.

```text
User action -> Activity method -> ViewModel state changes
                                      |
                                      v
Activity observes LiveData -> renders the current state
```

`LiveData` is lifecycle-aware. An observer receives updates while its owner is active, and the observer is removed when that lifecycle owner is destroyed. The screen does not need to manually subscribe and unsubscribe on every pause or resume.

## Create a ViewModel that exposes read-only state

The ViewModel keeps the mutable field private and exposes it as `LiveData`. UI code can observe the value without changing it directly. This example keeps a list in memory so the ownership and update flow are easy to see. Chapter 10 connects a screen to persistent storage.

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

`setValue()` updates LiveData on the main thread, which is appropriate for this button example. If a result arrives on a background thread, use `postValue()` to publish it back to observers. For a real list of notes, a repository should supply the records and assign persistent IDs. The in-memory counter is only for this small example.

The ViewModel has no reference to the activity, fragment, view, or `Context`. Those objects have shorter lifetimes. Keeping them out of the ViewModel avoids retaining a screen after Android has destroyed it.

## Get the ViewModel and observe its state

Create or retrieve the ViewModel through `ViewModelProvider`. Use the activity as the owner when the state belongs to the whole activity. Observing with the activity also means the observer is tied to that activity instance.

The screen layout from the previous chapter needs a title field and an Add button. They can sit above the existing list and empty state:

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

The updated values in `res/values/strings.xml` include the empty-state label from the previous chapter and the two new form labels:

```xml
<resources>
    <string name="no_study_notes">No study notes yet.</string>
    <string name="note_title_hint">Enter a study topic</string>
    <string name="add_note">Add note</string>
</resources>
```

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

The screen sets its adapter before observing. When LiveData emits the current list or a changed list, the observer submits it to the `ListAdapter` and updates the empty state. Pressing Add changes the ViewModel, not the adapter directly. That keeps one place responsible for the current list.

When Android recreates the activity after rotation, `ViewModelProvider` returns the same ViewModel instance for that activity's store. The new activity registers a new lifecycle-aware observer and receives the current value. When the activity is permanently finished, its ViewModel is cleared.

## Observe from a Fragment's view lifecycle

A fragment can stay on the back stack after its view has been destroyed. When observing state that updates fragment views, use `getViewLifecycleOwner()` rather than the fragment itself:

```java
viewModel.getNotes().observe(getViewLifecycleOwner(), notes -> {
    adapter.submitList(notes);
    emptyMessage.setVisibility(notes.isEmpty() ? View.VISIBLE : View.GONE);
});
```

Place this observer in `onViewCreated()`, after the fragment's views and adapter exist. The observer is removed with the fragment view, so it cannot try to update an old view after the fragment returns from the back stack.

## Keep work in the appropriate layer

The activity handles UI behavior: reading a field, reacting to a tap, showing a message, and navigating. The ViewModel owns screen state and decides how a user action changes that state. A repository coordinates data sources such as Room, a web service, or preferences. This separation makes each piece easier to understand and check.

```text
Activity or Fragment <-> ViewModel <-> Repository <-> Room or network
       UI events          screen state          app data
```

When a ViewModel needs a repository, give it that dependency through a `ViewModelProvider.Factory` or the project's dependency injection setup. The default provider in the example can create a ViewModel with a no-argument constructor. A constructor that requires a repository needs a factory that knows how to provide it.

Keep resource lookups and view updates in the UI. A ViewModel should not call `getString()` using an activity context or hold a `TextView`. It can expose a state value or a message identifier, and the activity can decide how to display it.

## Common state mistakes I check

- A ViewModel survives configuration changes, but it is not permanent storage.
- A value set with `setValue()` must be changed on the main thread. Use `postValue()` from a background thread.
- Expose `LiveData` to the screen and keep `MutableLiveData` private to the ViewModel.
- Do not keep an activity, fragment, view, or UI `Context` in a ViewModel.
- A fragment that observes view data should use `getViewLifecycleOwner()`.
- Let the ViewModel own the state change, then let the observer render the new state.
- Use Room or another persistent data source when values must remain after process death or an app restart.
- Use a factory or dependency injection when a ViewModel needs constructor dependencies.

## Practice: rotate the notes screen

Run the list example from the previous chapter. Move the list and Add button logic into `NotesViewModel`, observe its `LiveData`, and add two notes. Rotate the device and confirm that the items remain without being added a second time. Then close and reopen the app. The in-memory list should be gone, which makes the difference between ViewModel state and persistent storage visible.

## Notes to remember

- The ViewModel owns screen state across configuration changes.
- LiveData notifies active lifecycle owners and removes observers when they are destroyed.
- The activity sends actions to the ViewModel and renders observed state.
- A Fragment should observe view state with its view lifecycle owner.
- Room or another persistent store is needed for data that must survive process death.

## References

- [ViewModel overview for Views](https://developer.android.com/topic/libraries/architecture/views/viewmodel)
- [LiveData overview](https://developer.android.com/topic/libraries/architecture/livedata)
- [Save UI states for Views](https://developer.android.com/topic/libraries/architecture/views/saving-states-views)
- [UI layer](https://developer.android.com/topic/architecture/ui-layer)
- [Android architecture recommendations for Views](https://developer.android.com/topic/architecture/views/recommendations-views)

