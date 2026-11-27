# Android Keywords and Concepts

This file indexes Android platform terminology, development tools, APIs, and application architecture concepts used in these notes. Android is an operating-system platform, not a programming language, so this is not a list of reserved language words. Kotlin reserved words belong to the Kotlin language reference.

Android APIs and behavior can vary by API level, device, library, and target SDK. Use this index as a learning aid and check the version assumptions in the relevant chapter.

For the course outline and broad review, see [README.md](README.md) and [Android summary](android-summary.md).

## Platform and project terms

- **Android platform** - operating-system APIs and services used by Android applications.
- **Android SDK** - platform APIs, build tools, command-line tools, and related development components.
- **API level** - integer identifier for a platform API release.
- **minSdk** - minimum platform API level supported by an application.
- **targetSdk** - API level an application declares it is designed and tested against, with behavior and policy implications.
- **compileSdk** - API level used to compile the application.
- **Android Studio** - integrated development environment for Android applications.
- **Android Emulator** - virtual device implementation for running and testing Android.
- **AVD** - Android Virtual Device configuration for an emulator.
- **ADB** - Android Debug Bridge command-line tool for communicating with devices and emulators.
- **Android Runtime (ART)** - runtime environment that executes Android applications.
- **Linux kernel** - kernel foundation of the Android system.
- **Application sandbox** - isolation boundary for an app's process, files, and permissions.
- **Binder** - Android inter-process communication mechanism used by framework and apps.
- **Package name / application ID** - identifier used to identify an installed application; build configuration may define the final application ID.
- **Application manifest** - XML description of app components, capabilities, and configuration.
- **Resource** - non-code application asset such as a string, drawable, layout, or color.
- **Resource qualifier** - configuration suffix used to select resources for a device configuration, locale, density, or other property.
- **APK** - Android Package, an installable application archive.
- **Android App Bundle (AAB)** - publishing format from which distribution systems can generate device-specific APKs.
- **Android NDK** - native development tools for applications that use C or C++ code.
- **AndroidX** - package namespace for maintained Android support libraries.
- **Jetpack** - set of Android libraries and tools that support common development patterns.
- **Device compatibility** - ability of an application to operate across specified platform versions and device configurations.
- **Form factor** - device category such as phone, tablet, foldable, TV, or watch.

## App components and communication

- **Activity** - component that provides a user-interaction entry point, often a screen.
- **Activity lifecycle** - callbacks that describe creation, visibility, interaction, stopping, and destruction of an Activity.
- **Service** - component for selected work without a user interface, subject to platform execution policies.
- **Started service** - service started to perform work independently of a binding client, subject to current platform rules.
- **Bound service** - service that exposes an interface to components bound to it.
- **Foreground service** - service for qualifying ongoing work that must be visible to the user through a notification and comply with platform policy.
- **Broadcast receiver** - component that responds to selected broadcast messages.
- **Content provider** - component that exposes structured data through a URI-based contract.
- **ContentResolver** - API used by an app to interact with content providers.
- **Intent** - message describing an action and optional data used to activate components or request inter-app behavior.
- **Explicit intent** - intent that identifies a specific component.
- **Implicit intent** - intent that describes an action for the system to match to an eligible component.
- **Intent filter** - manifest declaration of the types of implicit intents a component can handle.
- **Intent extra** - additional value carried in an Intent.
- **URI** - identifier for content or a resource, commonly used in Android sharing and provider APIs.
- **Exported component** - component that may be started or accessed from outside the app, subject to platform rules and permissions.
- **Deep link** - link that opens a particular destination within an application.
- **Android App Link** - verified web link that can open an associated app.
- **Task** - collection of activities organized in a back stack for user navigation.
- **Back stack** - ordered history of activities in a task.
- **Process death** - termination of an app process by the system or another cause.
- **Configuration change** - change such as rotation, locale, or screen size that may recreate components.
- **Saved state** - small state retained across recreation using appropriate lifecycle mechanisms.
- **Process lifecycle** - system management of application processes based on component importance and resource needs.

## Kotlin and asynchronous programming

- **Kotlin** - programming language commonly used for modern Android development.
- **Null safety** - Kotlin type system features for representing values that may be absent.
- **Data class** - Kotlin class pattern for values with generated structural methods.
- **Sealed class or interface** - Kotlin type pattern for representing a restricted set of alternatives.
- **Extension function** - Kotlin function that adds callable behavior to an existing type without changing that type.
- **Coroutine** - Kotlin concurrency construct for asynchronous work.
- **Structured concurrency** - parent-child coroutine relationship that scopes cancellation and lifetime.
- **Coroutine scope** - object that provides a lifecycle and context for child coroutines.
- **Coroutine context** - metadata that controls coroutine execution, including dispatcher and job.
- **Dispatcher** - coroutine context element that controls where coroutine work executes.
- **Cancellation** - cooperative signal that requests a coroutine or operation to stop.
- **Flow** - Kotlin type for asynchronously emitting a sequence of values.
- **StateFlow** - state-holder flow that exposes a current value and subsequent updates.
- **SharedFlow** - configurable hot flow for broadcasting values to collectors.
- **Suspend function** - Kotlin function that can suspend without blocking its thread.
- **Main thread** - thread responsible for Android UI interactions; long blocking work can cause an unresponsive interface.
- **Lifecycle-aware collection** - observing asynchronous UI data in relation to the visible component's lifecycle.

## Jetpack Compose

- **Jetpack Compose** - Android declarative UI toolkit.
- **Composable function** - Kotlin function annotated to participate in Compose UI composition.
- **Composition** - process that builds and maintains a UI tree from composable functions.
- **Recomposition** - reevaluation of composables whose observed inputs or state have changed.
- **State** - observable value that can cause Compose to update affected UI.
- **State hoisting** - moving UI state to a caller so it can be shared or controlled explicitly.
- **Unidirectional data flow** - pattern where state flows toward UI and events flow back to a state holder.
- **remember** - Compose API for retaining a value across recompositions while its composition remains.
- **rememberSaveable** - Compose API for saving supported UI state across recreation using saved-state mechanisms.
- **Modifier** - chain of UI behavior and drawing configuration applied to a composable.
- **Side effect** - work that interacts with state outside the declarative UI description.
- **LaunchedEffect** - Compose effect API that launches coroutine work tied to composition keys and lifetime.
- **DisposableEffect** - Compose effect API for registering work that requires cleanup.
- **SideEffect** - Compose API for publishing a completed composition's state to non-Compose code.
- **Derived state** - state calculated from other state and observed only where needed.
- **CompositionLocal** - mechanism for providing values to descendants without passing them through every parameter.
- **Preview** - Android Studio rendering of a composable in a design-time configuration.
- **Semantics** - metadata exposed by UI elements for accessibility and testing.
- **Accessibility semantics** - role, label, state, and actions that help assistive technologies understand UI.
- **Layout** - measurement and placement of UI elements.
- **Lazy list** - Compose list that composes visible items as needed.
- **Material Design** - design system and component guidance used by Android UI libraries.
- **Material 3** - current generation of Material Design components and theming for Android libraries.
- **Theme** - colors, typography, shapes, and related design values applied consistently.
- **State restoration** - restoring a UI to a useful state after recreation or process loss.
- **Views** - traditional Android UI toolkit based on View and ViewGroup objects.
- **Compose interoperability** - techniques for combining Compose UI with existing Views-based UI.

## App architecture and data

- **UI state** - data needed to render a screen at a point in time.
- **UI event** - user or system action sent from UI to application logic.
- **ViewModel** - lifecycle-aware state holder for UI-related data and logic.
- **SavedStateHandle** - ViewModel-supported access to small values that can be restored after process recreation.
- **Repository** - abstraction that coordinates access to one or more data sources.
- **Data source** - source of app data, such as a remote service, database, file, or in-memory cache.
- **Domain layer** - optional layer for business rules and use cases.
- **Dependency injection** - providing dependencies to a component rather than constructing them throughout the code.
- **Room** - AndroidX library for structured local database access over SQLite.
- **SQLite** - embedded relational database engine commonly used for local app data.
- **Entity** - object or model representing a stored database record.
- **DAO** - data access object that defines database operations.
- **Migration** - controlled change to a database schema while preserving data.
- **DataStore** - AndroidX storage solution for typed settings or preference data.
- **Preference** - named setting stored for later use.
- **Cache** - data stored temporarily to improve access or reduce repeated work.
- **Serialization** - conversion between in-memory data and a stored or transmitted representation.
- **JSON** - text-based data interchange format commonly used by network APIs.
- **Offline-first** - design where local data can support useful behavior without a current network connection.

## Background work and notifications

- **WorkManager** - AndroidX API for scheduling deferrable, persistent work with constraints.
- **Worker** - WorkManager unit that performs a defined task.
- **WorkRequest** - description of work and its constraints or scheduling options.
- **Work constraint** - requirement such as network availability or charging state before work runs.
- **Unique work** - named WorkManager work that avoids unintended duplicate scheduling.
- **JobScheduler** - platform API for scheduling work under system-managed conditions.
- **AlarmManager** - platform API for time-based alarms subject to exact-alarm and background rules.
- **Notification** - user-visible message presented by the system.
- **Notification channel** - user-configurable category for app notifications on supported Android versions.
- **Foreground notification** - notification associated with a foreground service.
- **Doze** - power-saving behavior that can defer background activity.
- **App standby** - system behavior that can limit background work for apps the user does not use often.
- **Background execution limit** - platform restriction on work performed while an app is not active.
- **Periodic work** - recurring scheduled task subject to system scheduling constraints.
- **Retry policy** - rules for retrying failed background work.

## Permissions, privacy, and storage

- **Permission** - authorization required for selected app capabilities or protected data.
- **Normal permission** - permission category generally granted at install time, subject to current platform definitions.
- **Runtime permission** - permission requested from the user while the app is in use.
- **Signature permission** - permission category limited to apps signed by an allowed certificate or privileged relationship.
- **Permission rationale** - contextual explanation of why an app needs access.
- **Permission denial** - user choice that the app must handle without assuming access.
- **Permission revocation** - removal of a previously granted permission.
- **Scoped storage** - storage model that limits broad access to shared external storage.
- **App-specific storage** - storage area intended for files owned by one app.
- **Storage Access Framework (SAF)** - system interface for selecting and granting access to documents.
- **Photo Picker** - system UI for selecting visual media without broad library access.
- **Content URI** - URI that identifies data exposed through a content provider.
- **URI permission grant** - temporary or scoped authorization to access content through a URI.
- **Keystore** - secure key storage service for cryptographic keys under Android platform controls.
- **Personal data** - information that can identify or relate to a person and requires careful handling.
- **Data minimization** - collecting and retaining only data needed for a defined feature.
- **Backup policy** - configuration that controls which application data may be backed up.
- **Network security configuration** - declarative app policy for selected network security settings.

## Build system and dependencies

- **Gradle** - build automation system commonly used for Android projects.
- **Android Gradle Plugin (AGP)** - Gradle plugin that adds Android build and packaging tasks.
- **Gradle wrapper** - project files that select and invoke a Gradle distribution.
- **Build script** - Gradle configuration for plugins, dependencies, and project settings.
- **Kotlin DSL** - Kotlin-based syntax for Gradle configuration.
- **Groovy DSL** - Groovy-based syntax for Gradle configuration.
- **Settings file** - Gradle file defining included projects and build configuration.
- **Plugin** - reusable build logic that registers tasks and conventions.
- **Dependency** - library or module required by a project.
- **Repository** - configured source from which Gradle resolves dependencies.
- **Variant** - configured combination of build type and product flavor.
- **Build type** - build configuration such as debug or release.
- **Product flavor** - build configuration for a product edition or environment.
- **Source set** - collection of source and resources for a module or variant.
- **Build task** - unit of work performed by Gradle.
- **Manifest merger** - build process that combines manifests from app modules and dependencies.
- **R8** - Android code shrinker and optimizer used in release builds.
- **ProGuard rules** - configuration rules used to preserve or transform selected code during shrinking.

## Testing and diagnostics

- **Unit test** - test of a small logic unit without requiring a full Android device runtime.
- **Instrumented test** - test executed in an Android runtime on an emulator or device.
- **Compose UI test** - test that interacts with Compose UI semantics and verifies observable behavior.
- **Test rule** - setup helper that controls a test environment.
- **Mock** - test substitute for a dependency.
- **Fake** - simplified working implementation used in a test.
- **Logcat** - Android logging and diagnostic stream.
- **Debugger** - tool for pausing execution and inspecting program state.
- **Android Studio Profiler** - tools for analyzing app CPU, memory, network, and energy behavior.
- **ANR** - application-not-responding condition when the app fails to respond within a system-defined interval.
- **Crash report** - diagnostic record produced when app execution terminates due to an error.
- **StrictMode** - development tool that can detect selected main-thread and resource issues.
- **Macrobenchmark** - measurement approach for larger user flows and app performance.

## Security, release, and distribution

- **App signing** - cryptographic signing of an application artifact to identify its publisher and support updates.
- **Keystore** - protected file or key provider used for signing or cryptographic material.
- **Signing key** - private key used to sign application artifacts.
- **Version code** - monotonically increasing integer used for app version ordering by distribution systems.
- **Version name** - human-readable application version label.
- **Debug build** - development-oriented build with debugging features and non-production signing.
- **Release build** - distribution-oriented build with production configuration and signing.
- **APK** - installable Android package.
- **AAB** - Android App Bundle publishing format.
- **Code shrinking** - removing code and resources not needed by an application.
- **Obfuscation** - transforming names or structure to make reverse engineering harder.
- **Staged rollout** - distributing a release gradually to a subset of users.
- **Rollback** - stopping or reversing a problematic release.
- **Play Console** - service for managing Android app releases and distribution through Google Play.
- **Target API requirement** - distribution rule specifying a minimum target SDK level that can change over time.
- **Release track** - distribution channel for testing or releasing an app.
