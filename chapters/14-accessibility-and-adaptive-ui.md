# 14. Accessibility and adaptive layouts

[Back to notes index](../README.md)

[Previous: Permissions and Android device features](13-permissions-and-device-features.md) | [Notes index](../README.md) | [Next: Debugging, testing, and release](15-debugging-testing-and-release.md)

Accessibility and adaptive design make an app easier to use with different abilities, input methods, font settings, and window sizes. These notes focus on Java and XML layouts built with Android Views.

## 1. Accessibility is part of the interface

An interface is not accessible just because its text is visible. A person may use TalkBack to hear and navigate the screen, a keyboard or D-pad to move between controls, Switch Access to select items, or a large font setting to read content. A good layout keeps its meaning and actions clear for each of these ways of using the app.

Use standard Android controls when they fit the job. A `Button` already exposes button behavior to accessibility services. A clickable `TextView` may look similar, but it does not automatically provide the same role or interaction. Keep a visible label next to an icon when space allows, and do not communicate state with color alone.

## 2. Give controls useful spoken names

Text controls such as `TextView`, `Button`, and `EditText` already expose their text to screen readers. Add `contentDescription` when a graphic or icon-only control has meaning that is not present in visible text. Describe the action or purpose, not the widget type.

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

Put user-facing descriptions in string resources so they can be translated:

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

Do not add a spoken label to decorative artwork. Mark it as unimportant for accessibility instead:

```xml
<ImageView
    android:layout_width="match_parent"
    android:layout_height="160dp"
    android:importantForAccessibility="no"
    android:src="@drawable/header_texture"
    android:contentDescription="@null" />
```

Avoid repeating nearby text. If a row already displays “Settings” beside a gear icon, a second spoken “Settings” description on the icon can make TalkBack announce the same information twice. In a repeated list, each actionable item needs enough unique text to tell it apart, such as the note title rather than the same generic word “Open.”

Associate form labels with their fields. A hint can disappear when the user types, so it should not be the only label:

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

## 3. Make touch targets easy to reach

Android recommends a focusable touch area of at least **48dp by 48dp** for each interactive control. The drawn icon can be smaller if padding provides the larger target. Give neighboring controls enough space so a tap does not activate the wrong one.

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

For a `RecyclerView` row, make the clickable row or its action controls large enough, not just the tiny icon inside it. Use `dp` for layout dimensions and `sp` for text sizes. Avoid fixed heights around text because large font settings can make the text clip or overlap.

## 4. Keep reading and focus order clear

TalkBack moves through accessible elements in a traversal order. Keyboard and D-pad input move through views with input focus. These are related but distinct focus systems. A sensible XML order, clear grouping, and standard controls usually produce a useful order without extra focus code.

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

Use `nextFocusForward`, `nextFocusUp`, `nextFocusDown`, `nextFocusLeft`, or `nextFocusRight` only when the natural layout order does not match the intended navigation. Check that focus is visible and never trapped. Test that Tab, arrow keys, Enter, and D-pad center reach and activate the controls on devices that support them.

## 5. Let text and contrast settings work

Use `sp` for text so the system font-size setting can scale it. Prefer `wrap_content` for text containers, allow long labels to wrap, and check that dialogs and buttons still fit when the font is enlarged. Avoid placing essential text inside an image.

Text should have clear contrast against its background. Color can reinforce a status, but include another cue such as a label, icon, or shape. For example, show “Error” with an error icon and message instead of relying only on a red border. Check screens with Android Accessibility Scanner and inspect them at larger font sizes and display sizes.

## 6. Understand responsive and adaptive layouts

**Responsive** layouts resize and reflow the same interface as the available space changes. **Adaptive** layouts choose a different arrangement when there is enough space for it. A notes app might show one list on a narrow window and place the list beside the selected note on a wider window.

Use flexible dimensions such as `match_parent`, `wrap_content`, weights, and `ConstraintLayout` constraints rather than assuming a particular pixel resolution. Design for the app window, which can change on tablets, foldables, desktop windows, and split-screen mode. Do not assume that every device is a phone in portrait orientation.

Android can select alternative XML files using resource directory qualifiers. For example, `layout-w600dp` is selected when the app window has at least 600dp of available width. The base layout remains the fallback for narrower windows. Use the same file name and keep important view IDs consistent so the same Activity can work with either layout.

```text
res/
  layout/
    activity_notes.xml
  layout-w600dp/
    activity_notes.xml
```

The narrow layout can show a single list:

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

The wide layout can keep the list visible beside a detail panel:

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

The example shows the layout choice, not the list selection behavior. Java code still needs to handle a selected note and update the detail panel. Since both resources use the same `notesList` ID, the Activity can find that view in either arrangement. The detail container exists only in the wide resource, so Java should check whether it is present before using it:

```java
FrameLayout detailContainer = findViewById(R.id.noteDetailContainer);

if (detailContainer != null) {
    // A wide layout is active, so show the selected note here.
} else {
    // A narrow layout can open the selected note on a separate screen.
}
```

The `sw600dp` qualifier means the smallest width of the available app area is at least 600dp, regardless of current orientation. The `w600dp` qualifier means the current available width is at least 600dp, which can be more useful when an app window is resized. Pick the qualifier based on the layout decision, and test window resizing rather than relying only on device names.

## 7. Check accessibility on a real screen

Use this quick review while building each screen:

1. Turn on TalkBack and move through the screen. Confirm every action has a clear name and state, and repeated rows can be distinguished.
2. Use keyboard or D-pad navigation. Confirm focus order follows the task and focus remains visible.
3. Increase font and display sizes. Confirm text wraps, controls remain reachable, and content does not overlap or disappear.
4. Inspect touch target sizes and text contrast with Accessibility Scanner, then correct the underlying layout or labels.
5. Resize the emulator or use split-screen. Confirm the layout changes when space allows and the current selection or typed text is retained.

Automated checks can catch some issues, but manually using the screen with accessibility features enabled reveals confusing labels, order, and behavior that a static scan cannot judge.

## 8. Key points

- Keep visible text on text controls. Add a content description for meaningful icon-only controls and images.
- Hide purely decorative graphics from accessibility services.
- Aim for at least 48dp by 48dp of touch area and give nearby actions enough separation.
- Use standard Android controls and a logical focus order; test TalkBack and keyboard input.
- Use `sp` for text and let content grow when users enlarge fonts.
- Build around available window space. Provide an alternate layout when a wider arrangement helps.
- Keep shared view IDs stable across alternate layout resources and handle views that exist only in one variant.

## References

- [Make apps more accessible with Views](https://developer.android.com/guide/topics/ui/accessibility/views/apps-views)
- [Principles for improving app accessibility with Views](https://developer.android.com/guide/topics/ui/accessibility/views/principles-views)
- [Responsive and adaptive design with Views](https://developer.android.com/develop/ui/views/layout/responsive-adaptive-design-with-views)
- [Window size classes for Views](https://developer.android.com/develop/ui/views/layout/use-window-size-classes)
- [Input compatibility on large screens](https://developer.android.com/develop/ui/views/touch-and-input/input-compatibility-on-large-screens)

---

[Previous: Permissions and Android device features](13-permissions-and-device-features.md) | [Notes index](../README.md) | [Next: Debugging, testing, and release](15-debugging-testing-and-release.md)

