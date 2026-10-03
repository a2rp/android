# 17. Complete Q&A

[Back to notes index](../README.md)

| [Previous: All code samples](98-all-code-samples.md) | [Notes index](../README.md) | [Next: Notes index](../README.md) |
|:--|:--:|--:|

This chapter collects questions that come up while studying Android app development with Java and XML. Answers are kept short enough to review, with links to the topic notes when a deeper explanation or example is useful.

## 1. First Android app

[Related notes](01-first-android-app.md)

### 1.1 What does Android Studio do?

Android Studio is the IDE used to edit, build, run, and debug Android projects. It connects project code to the Android SDK, Gradle, emulators, and device tools.

### 1.2 What is an Activity?

An Activity is an Android app component that provides a window for a user-facing screen. It owns a lifecycle, receives system events, and can host a view hierarchy.

### 1.3 What is the Android manifest for?

`AndroidManifest.xml` identifies app components and declares app-level information such as permissions and supported device features. Android reads it when installing and launching the app.

### 1.4 What is Gradle's role?

Gradle builds the app by compiling Java, processing resources, resolving dependencies, and packaging the result. The Gradle wrapper keeps the project tied to a known Gradle version.

### 1.5 What is the difference between an emulator and a physical device?

Both can install and run the app. An emulator is useful for repeatable screen sizes and Android versions; a physical device helps verify real hardware, manufacturer behavior, and performance.

### 1.6 What does a successful build prove?

A successful build proves that the selected variant compiled and was packaged. It does not prove the app behaves correctly, so launch it and check the screen and important interactions as well.

## 2. Java essentials

[Related notes](02-java-for-android.md)

### 2.1 What is the difference between a primitive and an object reference?

A primitive such as `int` stores a simple value. A reference variable points to an object, or can be `null` when it points to no object.

### 2.2 What is the difference between a class and an object?

A class describes fields and behavior. An object is one instance of that class with its own state, created with `new` or by a framework.

### 2.3 Why use encapsulation?

Encapsulation keeps an object's state behind methods that can validate changes. It reduces accidental updates and makes the class's rules easier to understand.

### 2.4 What is an interface useful for?

An interface defines behavior that different classes can implement. Android listeners use this idea to let a view report an event without knowing all the work the app will do in response.

### 2.5 When should `equals()` be used instead of `==`?

For objects, `==` checks whether two references point to the same object. `equals()` checks logical equality when the class implements that comparison, as `String` does.

### 2.6 How should Java code handle `null` and exceptions?

Check whether a value may be absent before calling its methods, and validate input at the boundary where it enters the app. Catch exceptions where the app can recover or show a useful error, not just to hide a failure.

## 3. Project structure and resources

[Related notes](03-project-structure-and-resources.md)

### 3.1 What is an app module?

An app module contains the Android app's source, resources, manifest, and module-level build configuration. A project can contain other modules such as reusable libraries.

### 3.2 What is the difference between `namespace` and `applicationId`?

The `namespace` determines the package for generated code such as `R`. The `applicationId` identifies the installed app to Android and app stores. They often match, but they serve different purposes.

### 3.3 What do `minSdk`, `targetSdk`, and `compileSdk` mean?

`minSdk` is the oldest Android API level the app supports. `targetSdk` opts the app into behavior associated with a platform version. `compileSdk` selects the Android APIs available while compiling.

### 3.4 Why put text in `strings.xml`?

String resources separate user-facing text from Java and layouts. They support localization, reuse, and clearer accessibility labels.

### 3.5 What is `R`?

`R` is generated code that gives Java references to resources such as `R.id.saveButton` and `R.string.app_name`. Do not edit it by hand; fix resource names or XML errors that prevent it from generating.

### 3.6 What is a build variant?

A build variant combines a build type, such as `debug` or `release`, with a product flavor when the project defines flavors. Variants can use different settings or resources for development and distribution.

## 4. Activities and lifecycle

[Related notes](04-activities-and-lifecycle.md)

### 4.1 What does the Activity lifecycle describe?

It describes how an Activity is created, becomes visible and interactive, loses focus, stops, and may be destroyed. The system can trigger transitions when the user navigates or the device configuration changes.

### 4.2 What is the usual order when an Activity first opens?

The common sequence is `onCreate()`, `onStart()`, then `onResume()`. The Activity is ready for user interaction after `onResume()` completes.

### 4.3 What happens during a rotation?

By default, a configuration change such as rotation can destroy and recreate the Activity. UI state must be restored or stored outside the Activity if it needs to survive recreation.

### 4.4 What belongs in `onSaveInstanceState()`?

Small, temporary UI state that helps restore the current screen, such as a selected tab or text query, can go in the saved-state `Bundle`. Long-term app data belongs in persistent storage.

### 4.5 Is `onDestroy()` guaranteed to run when the process ends?

No. The system can kill an app process without calling `onDestroy()`. Save important state before the app reaches that point instead of treating destruction as a reliable save callback.

### 4.6 Which Context should a ViewModel keep?

An Activity or View Context should not be stored in a regular ViewModel because the ViewModel can outlive that screen and cause a memory leak. Use a repository or another lifecycle-appropriate owner for work that needs application-level access.

## 5. XML layouts and View Binding

[Related notes](05-xml-layouts-and-viewbinding.md)

### 5.1 What is a View hierarchy?

A layout is a tree of Views. Containers such as `LinearLayout` and `ConstraintLayout` position child Views, while widgets such as `TextView` and `Button` display content or accept input.

### 5.2 When is `ConstraintLayout` useful?

It positions Views through relationships to the parent or to other Views. It can express a flexible screen with fewer nested containers than a chain of layouts.

### 5.3 What is the difference between `dp` and `sp`?

Use `dp` for layout dimensions that should account for screen density. Use `sp` for text so the user's font-size setting can scale it.

### 5.4 What does `wrap_content` mean?

The View asks for enough space to display its content. It is often appropriate for text and buttons, while `match_parent` fills the available size in that dimension.

### 5.5 What does View Binding provide?

View Binding generates a binding class for each layout and gives typed references to its Views. It avoids repeated `findViewById()` calls and many invalid-ID casts.

### 5.6 What is the difference between a style and a theme?

A style describes attributes for a View or group of Views. A theme supplies attributes across an Activity or app, such as colors and default widget appearances.

## 6. Events, forms, and validation

[Related notes](06-events-forms-and-validation.md)

### 6.1 What is an event listener?

A listener is a callback that a View invokes when an event occurs, such as a click. The Activity or another controller can respond without the View containing the whole app workflow.

### 6.2 What does `inputType` do on an `EditText`?

It describes the expected input, such as email or a number, so Android can choose a suitable keyboard and input behavior. It does not replace validating the value in Java.

### 6.3 Where should form validation happen?

Validate close to the point where input enters the app and validate again before saving or submitting. The UI can show early feedback, while the data layer should still protect important rules.

### 6.4 When should a form show an error?

Show an error when the user submits invalid input, or after a field has been edited enough to make immediate feedback useful. Explain what needs to change and keep valid input intact.

### 6.5 What is `TextWatcher` for?

`TextWatcher` observes text changes while the user edits a field. It is useful for updating dependent UI, but expensive work should not run on every keystroke.

### 6.6 How can an app avoid duplicate form submissions?

Disable or otherwise guard the submit action while a request is in progress, then restore it when the operation completes. The server or data layer should also handle retries safely when duplicate operations would matter.

## 7. Intents, fragments, and navigation

[Related notes](07-navigation-intents-and-fragments.md)

### 7.1 What does an Intent represent?

An Intent describes an operation Android should perform, often opening an Activity. An explicit Intent names a target component; an implicit Intent describes an action that another app may handle.

### 7.2 When is an implicit Intent useful?

It is useful when asking another installed app to handle a standard action, such as viewing a web link or sharing text. Check that a handler is available when the app needs to avoid an unhandled launch.

### 7.3 How should small values be passed between Activities?

Add simple values to the Intent extras with a key, then read them in the destination Activity. Keep the data small and make the destination handle a missing or invalid extra.

### 7.4 What is the difference between an Activity and a Fragment?

An Activity is an Android component with a window and lifecycle. A Fragment is a reusable part of a screen hosted by an Activity or another Fragment, with its own view lifecycle.

### 7.5 What does the Fragment back stack do?

The `FragmentManager` can record fragment transactions so Back reverses an earlier navigation step. Add a transaction to the back stack only when that screen should be restored by Back.

### 7.6 How should a Fragment access its View after `onDestroyView()`?

It should not keep using the old View. Clear View Binding references in `onDestroyView()` and observe screen state with the Fragment's view lifecycle owner.

## 8. RecyclerView and adapters

[Related notes](08-recyclerview-and-adapters.md)

### 8.1 Why use RecyclerView for a changing list?

RecyclerView reuses item Views as they move on and off screen. This avoids creating a separate View for every row and supports large or changing collections more efficiently.

### 8.2 What does a ViewHolder do?

A ViewHolder keeps references to the Views in one row. The adapter binds the current data item into that holder as RecyclerView reuses it.

### 8.3 What should `onBindViewHolder()` do?

It should set all visible row state from the current item, including text, selected state, and visibility. Reset every state that can differ so reused rows do not show values from an earlier item.

### 8.4 Why should a click listener use the current bound item?

RecyclerView can move items after the listener is created. Read the current binding position when needed, check that it is valid, and pass the corresponding model or stable item ID to the screen callback.

### 8.5 Which layout manager should a list use?

Use `LinearLayoutManager` for a vertical or horizontal list, `GridLayoutManager` for a grid, and `StaggeredGridLayoutManager` when items have varying sizes. Choose based on the visual arrangement the screen needs.

### 8.6 Why can `notifyDataSetChanged()` be a poor default?

It tells RecyclerView that the entire list may have changed, so it cannot make precise updates or preserve all item animations. For small examples it can be acceptable; for regular updates, calculate and dispatch the changed items.

## 9. ViewModel, LiveData, and app architecture

[Related notes](09-architecture-viewmodel-livedata.md)

### 9.1 What problem does a ViewModel solve?

A ViewModel keeps screen state across Activity recreation, such as rotation. It separates that state from the Activity instance that draws the screen.

### 9.2 What is LiveData?

LiveData is an observable holder that can notify lifecycle-aware observers when its value changes. Observers tied to an Activity or Fragment lifecycle stop receiving updates when that owner is inactive.

### 9.3 Why expose `LiveData` instead of `MutableLiveData`?

The ViewModel can keep a mutable reference to update state while exposing a read-only `LiveData` reference to the UI. This keeps the UI from changing state outside the intended methods.

### 9.4 What does a repository do?

A repository provides a consistent place for the rest of the app to request data. It can decide whether data comes from Room, a network source, or another store without making the Activity manage those details.

### 9.5 Should a ViewModel keep an Activity or Fragment?

No. A ViewModel may outlive the screen instance, so retaining a screen Context can leak it. Keep screen-specific work in the screen and pass only the state or dependencies the ViewModel needs.

### 9.6 What happens if an observer is attached to the wrong owner in a Fragment?

Observing with the Fragment instead of its view lifecycle can keep view observers alive after `onDestroyView()`. Observe UI state with `getViewLifecycleOwner()` so the observer follows the current View.

## 10. Room and local preferences

[Related notes](10-local-data-room-and-preferences.md)

### 10.1 What are the main Room pieces?

An `@Entity` describes a table, a `@Dao` describes database operations, and an `@Database` class connects the database and its entities. Room generates the implementation and checks queries at compile time.

### 10.2 What should a DAO contain?

A DAO contains methods for queries and data changes, such as insert, update, delete, or observing rows. Keeping SQL in the DAO avoids scattering database statements through Activities.

### 10.3 Why should database operations stay off the main thread?

Database work can take long enough to block drawing and input. Run it on a background executor or through the asynchronous API chosen for the project, then publish the result to the UI.

### 10.4 What is a Room migration?

A migration describes how to change an existing database schema while preserving stored data. When adding or changing an entity, add and test the matching migration rather than assuming every user starts with an empty database.

### 10.5 When are preferences appropriate?

Preferences are suitable for small key-value settings, such as a selected display option. Use a database for structured, searchable records such as a collection of study notes.

### 10.6 What is the difference between `apply()` and `commit()` in SharedPreferences?

`apply()` updates the in-memory value immediately and writes it asynchronously. `commit()` writes synchronously and returns whether it succeeded, so calling it on the main thread can block the UI.

## 11. Networking, REST, and JSON

[Related notes](11-networking-and-rest-apis.md)

### 11.1 What is a REST API?

A REST-style API exposes resources through HTTP endpoints and uses methods such as GET to read or POST to submit data. The response commonly contains a status code, headers, and a body such as JSON.

### 11.2 Does `INTERNET` need a runtime permission prompt?

No. Declare `android.permission.INTERNET` in the manifest. It is a normal permission granted at install time, not a dangerous permission requested with a runtime dialog.

### 11.3 Why must a network request not run on the main thread?

Network response time is unpredictable. Blocking the main thread can freeze drawing and input, and Android may report an application-not-responding error. Use a background executor or an appropriate networking library.

### 11.4 What should an HTTP client check before parsing the body?

Check the status code and handle success and error responses intentionally. Configure connection and read timeouts, close streams and connections, and show a recoverable error when the request fails.

### 11.5 What is JSON parsing?

Parsing reads the JSON structure and converts its fields into values or model objects. Match the actual response shape and handle missing, null, or changed fields without crashing the screen.

### 11.6 Where should networking code live?

Keep HTTP details behind a data source or repository rather than inside an Activity. The UI can then display loading, success, and error state without managing sockets or response parsing.

## 12. Background work and notifications

[Related notes](12-background-work-and-notifications.md)

### 12.1 When is WorkManager appropriate?

Use WorkManager for deferrable work that should eventually run and may need constraints, retries, or persistence across app restarts. It is not meant to provide an exact run time.

### 12.2 When is a foreground service appropriate?

Use a foreground service for ongoing work that is noticeable to the user and must continue while the app is not in the foreground, such as a user-started recording or navigation session. Current Android versions impose service types, permission rules, and start restrictions, so check the platform requirements for the target SDK.

### 12.3 When is an alarm appropriate?

Use `AlarmManager` when work must be triggered around a particular clock time. Exact alarms are restricted for many apps, so request exact scheduling only when it is essential to the app's core function.

### 12.4 Why does Android use notification channels?

On Android 8.0 and later, notifications belong to a channel. Users can control channel behavior, and the app must create the channel before posting notifications through it.

### 12.5 What is `PendingIntent` used for in a notification?

A `PendingIntent` gives the system a token to perform an app action later, such as opening an Activity when a notification is tapped. Use the appropriate mutability flag and make the intent explicit when the action targets an app component.

### 12.6 When should the app request notification permission?

On Android versions that require it, request `POST_NOTIFICATIONS` at runtime when the user can understand the benefit. If permission is denied, the rest of the app should continue to work where possible.

## 13. Permissions, privacy, and device features

[Related notes](13-permissions-and-device-features.md)

### 13.1 What is the difference between a normal and dangerous permission?

A normal permission provides access Android considers low risk and is granted at install time. A dangerous permission protects more sensitive data or actions and requires a runtime decision on supported Android versions.

### 13.2 When should a runtime permission be requested?

Ask when the user starts a feature that needs the permission, and explain the purpose in context. Avoid requesting access at app launch before the user understands why it is needed.

### 13.3 What should happen when a user denies permission?

Keep the rest of the app usable and explain which feature is unavailable. If access can be requested again, let the user retry when appropriate; do not repeatedly interrupt after a clear refusal.

### 13.4 How should an app handle optional hardware?

Declare optional hardware with `required="false"` when the app can still work without it, then check for the feature before using it. Hide or disable the related action and provide a clear fallback.

### 13.5 Why prefer Android's Photo Picker for selecting media?

The Photo Picker lets the user choose specific media without granting broad access to the photo library. Request broader storage access only if the app's core feature truly needs it.

### 13.6 What is approximate location?

Approximate location gives a less precise location than precise access. If a feature can work with approximate location, support that choice instead of requiring a more precise permission than necessary.

## 14. Accessibility and adaptive layouts

[Related notes](14-accessibility-and-adaptive-ui.md)

### 14.1 Which controls need a content description?

Meaningful images and icon-only controls need a short description of their purpose. Text controls already expose their visible text, and decorative graphics should normally be hidden from accessibility services.

### 14.2 What touch target size should an interactive View provide?

Android recommends at least 48dp by 48dp of focusable touch area. The visible icon can be smaller if the View's minimum size and padding create a large enough target.

### 14.3 Why use `sp` for text?

`sp` respects the user's font-size preference. Fixed-height containers and text measured only in `dp` can clip content when a user enlarges text.

### 14.4 What is the difference between input focus and accessibility focus?

Input focus determines which View receives keyboard or D-pad input. Accessibility focus is the element a screen reader is currently exploring. They are separate states and should both lead to understandable navigation.

### 14.5 What is the difference between responsive and adaptive layout?

A responsive layout resizes or reflows as space changes. An adaptive layout selects a different arrangement when the available space supports it, such as placing a list and selected item side by side.

### 14.6 When should `layout-w600dp` be used?

Use it when an alternate layout should appear once the app window has at least 600dp of current available width. `layout-sw600dp` instead selects based on the smallest available width, regardless of current orientation.

## 15. Testing, debugging, and release

[Related notes](15-debugging-testing-and-release.md)

### 15.1 What belongs in a local unit test?

Test Java logic that does not require the Android framework, such as validation and formatting. These tests run on the local JVM and are usually quicker than device tests.

### 15.2 When is an instrumented test needed?

Use an instrumented test when the behavior needs Android or a real view hierarchy, such as clicking a button in an Activity or verifying an integration on an emulator.

### 15.3 What is the first useful clue in a crash stack trace?

Read the exception type and message, then find the first stack-trace frame that points into app code. That line identifies where the failure surfaced; inspect the preceding inputs and calls to find its cause.

### 15.4 Why test a release build if the debug build works?

Release configuration may enable code optimization, resource shrinking, and different settings. These can expose missing keep rules or assumptions that debug builds do not exercise.

### 15.5 What is the difference between an upload key and an app signing key?

The upload key signs the artifact sent to Google Play. With Play App Signing, Google Play uses the app signing key for APKs delivered to users. Keep private key files and passwords secure.

### 15.6 What is an Android App Bundle?

An Android App Bundle is a publishing format that contains compiled app code and resources. Google Play can use it to generate APKs suited to each device configuration.

## Final review

These questions revisit the ideas that connect the chapters: keep UI state with the right lifecycle owner, keep I/O off the main thread, ask for only the access a feature needs, and verify behavior on the kinds of screens and Android versions the app supports.

---

| [Previous: All code samples](98-all-code-samples.md) | [Notes index](../README.md) | [Next: Notes index](../README.md) |
|:--|:--:|--:|

