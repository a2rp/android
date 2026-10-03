# 8. Lists with RecyclerView and adapters

[Back to notes index](../README.md)

| [Previous: Intents, fragments, and navigation](07-navigation-intents-and-fragments.md) | [Notes index](../README.md) | [Next: App architecture with ViewModel and LiveData](09-architecture-viewmodel-livedata.md) |
|:--|:--:|--:|

## How RecyclerView displays a changing list

`RecyclerView` displays a collection of items without creating a permanent view for every row. As a row moves off screen, its view can be reused for another item. This keeps scrolling lists more efficient than building a large fixed set of views.

Four pieces work together:

| Part | Responsibility |
| --- | --- |
| `RecyclerView` | The scrolling container that requests rows. |
| `Adapter` | Creates row holders and binds each row to an item. |
| `ViewHolder` | Holds references to the views in one row. |
| `LayoutManager` | Decides whether rows appear in a vertical list, horizontal list, or grid. |

The usual sequence is: provide a layout manager, attach an adapter, and submit data. The adapter inflates a row when needed and fills it with the item for that position.

Add AndroidX RecyclerView to the app module using the project's version catalog or dependency conventions. The artifact is `androidx.recyclerview:recyclerview`. Keep dependency versions in one place when the project uses a version catalog.

## Model the rows as data

This example keeps a few study notes in a small immutable Java class. Each item has a stable ID, a title, and a summary. A stable ID lets the adapter recognize the same note after the list changes.

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

Keeping the fields final makes it harder to accidentally change data after submitting a list. When the title or summary needs to change, create a new `StudyNote` with the same ID and the new values.

## Define the screen and one row

The screen layout contains the list and an empty message. The empty message starts hidden and becomes visible when there are no items.

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

Each row is a separate layout file, here named `item_study_note.xml`:

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

The matching string belongs in `res/values/strings.xml`:

```xml
<resources>
    <string name="no_study_notes">No study notes yet.</string>
</resources>
```

## Bind each StudyNote in an adapter

`onCreateViewHolder()` inflates a row. `onBindViewHolder()` fills it with one item, and `getItemCount()` reports how many items are currently in the list. `ListAdapter` adds background diff calculation so the adapter can update only the rows that changed.

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

`areItemsTheSame()` compares identity, which is the note ID. `areContentsTheSame()` compares the values shown by this row. If the visible values change, the adapter rebinds the row. If another property is added to the row later, include it in the content comparison.

The click callback asks the holder for its current adapter position when tapped. Positions can change after a list update, so code should check for `RecyclerView.NO_POSITION` before looking up an item. Passing the `StudyNote` to the listener keeps the screen from depending on a temporary position.

## Attach the adapter and provide sample data

The activity configures a `LinearLayoutManager`, creates the adapter, and submits a list. A `GridLayoutManager` can be used instead when the screen needs a grid.

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

The `activity_notes.xml` file is the screen layout shown earlier. The example keeps data in memory so the list mechanics are visible. In an app, a repository or a `ViewModel` can provide the list; a later chapter connects persistent data and UI state.

When the list changes, make a new list and call `submitList()` again:

```java
List<StudyNote> updatedNotes = new ArrayList<>(adapter.getCurrentList());
updatedNotes.add(new StudyNote(4L, "Activities", "Lifecycle and state"));
adapter.submitList(updatedNotes);
```

Do not modify the list instance after giving it to a `ListAdapter`. `DiffUtil` assumes submitted lists and their items remain unchanged while it compares them. Use a new list for each update. For a plain `RecyclerView.Adapter`, call the matching notification method after a data change, such as `notifyItemInserted(position)`. Avoid `notifyDataSetChanged()` for routine changes because it discards precise update information.

## Choose a layout manager

| Layout manager | Arrangement | Example |
| --- | --- | --- |
| `LinearLayoutManager` | One vertical or horizontal row of items. | Notes, messages, settings. |
| `GridLayoutManager` | A grid with a chosen number of columns. | Photos or tiles. |
| `StaggeredGridLayoutManager` | Grid rows can have different item heights. | Cards with uneven content. |

```java
notesList.setLayoutManager(new LinearLayoutManager(this));
```

For a horizontal row, pass `RecyclerView.HORIZONTAL` as the orientation. A grid can be set up with `new GridLayoutManager(this, 2)` for two columns. Pick a layout manager based on how the items should read and scroll.

## Common list mistakes I check

- Give every row field a value on every bind. A recycled row may contain text from a previous item.
- Keep database and network work out of `onBindViewHolder()`. Binding runs often while scrolling.
- Use a stable item ID for diff identity. Do not use the current row position as the identity.
- Avoid keeping a position captured long before a click. Ask the holder for its current position and check `NO_POSITION`.
- Submit a new list instead of mutating the list already in `ListAdapter`.
- Show an empty state when there are no items, and update it when the data changes.
- Prefer item-level updates from `ListAdapter` or precise adapter notifications over refreshing the whole list.
- Test long titles and summaries so row content remains readable at larger text sizes.

## Practice: update the study list

Run the screen with the three sample notes. Tap one row and check that the selected note ID reaches the detail screen. Add a fourth item by submitting a new list, then change the title of an item while keeping its ID. Confirm the right row changes without replacing the full list. Finally, submit an empty list and check the empty message.

## Notes to remember

- `RecyclerView` reuses row views as items move on and off screen.
- The adapter creates holders, binds data, and reports the item count.
- The layout manager controls the list or grid arrangement.
- `ListAdapter` and `DiffUtil` identify changed rows when new immutable lists are submitted.
- A row click should return the current item, not depend on a stale position.
- Keep row binding quick and complete so recycled views show the correct content.

## References

- [Create dynamic lists with RecyclerView](https://developer.android.com/develop/ui/views/layout/recyclerview)
- [Customize a dynamic list](https://developer.android.com/develop/ui/views/layout/recyclerview-custom)
- [ListAdapter reference](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter)
- [DiffUtil reference](https://developer.android.com/reference/androidx/recyclerview/widget/DiffUtil)

