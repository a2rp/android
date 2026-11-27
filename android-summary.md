# Android Summary

A topic-wide review of Android application development and platform behavior. The README roadmap expands these ideas into lessons, Q&A, and practical examples.

Android evolves continuously. SDK levels, permission behavior, background-work limits, libraries, build tools, and publishing requirements can change. The detailed chapters should identify the platform and tool versions that matter to each procedure.

## 1. Android platform model

Android is a software platform built on a Linux-based system. Apps use Android framework APIs and libraries to interact with system capabilities, device hardware, and other applications. Each app normally runs under its own application identity and process, subject to system lifecycle and resource management.

A project includes source code, resources, a manifest, build configuration, and dependencies. The build system compiles and packages these inputs into an installable application artifact. A running app may be stopped by the system; process memory is not durable storage.

The modern course path is Kotlin-first and Compose-focused. Java and the Views UI toolkit remain relevant for existing applications and interoperability.

## 2. Android development environment

Android Studio provides project creation, code editing, build integration, device tools, debugging, and profiling. An Android SDK installation contains platform APIs, build tools, command-line tools, and emulator components selected for a project.

An emulator is a configurable virtual Android device. A physical device can expose hardware-specific behavior that an emulator does not reproduce exactly. Test screen sizes, API levels, permissions, network conditions, and process recreation as needed.

A typical project includes:

- A settings file that defines the build and included modules.
- Gradle build files that apply plugins and configure dependencies.
- Application source and resource directories.
- A manifest describing the app and its components.
- Build outputs for debug, release, and configured product variants.

## 3. Kotlin and Android APIs

Kotlin provides nullability, data classes, sealed types, extension functions, lambdas, and coroutines that are useful in Android applications. Platform APIs may expose Java types and callbacks, so Kotlin code must understand Java interoperability and platform types.

Prefer explicit null handling, immutable data where practical, and narrow interfaces. Separate application logic from Android framework types when it improves testing and reuse. Use a supported language and library version for the project's Android Gradle Plugin and toolchain.

## 4. App manifest and resources

The manifest declares application-level information, permissions, and components that the Android system can activate. Activities, services, receivers, and content providers may need manifest declarations. Component exposure and intent filters affect how other apps or the system can reach a component.

Resources externalize strings, colors, dimensions, layouts, icons, and other assets. Resource qualifiers can provide alternatives for locale, screen density, orientation, and other device configurations. Keep user-visible text out of code when localization is expected.

Treat manifest settings as security and compatibility decisions. Review exported components, permissions, backup configuration, network policy, and app links for the target SDK and platform behavior.

## 5. Application components and intents

Android's main component types are:

- Activity - an entry point for user interaction and screens.
- Service - a component for operations that may continue without a UI, subject to platform execution rules.
- Broadcast receiver - an entry point for selected broadcasts and events.
- Content provider - a structured interface for sharing data through a URI-based contract.

An Intent communicates an action and optional data to another component. Explicit intents identify a target component. Implicit intents describe an action that the system can match to an eligible component. Validate data crossing component boundaries and avoid exposing internal components unnecessarily.

A service is not automatically a background thread. Long-running, deferrable, and user-visible background work have different platform expectations and should use the appropriate API.

## 6. Lifecycle and process state

Activities and other components have lifecycle callbacks that describe changes in visibility, interaction, or system ownership. Rotation, multi-window use, navigation, memory pressure, and process death can recreate UI components.

Do not store important state only in a field on an Activity or Composable. Use saved state for small state needed across recreation and persistent storage for durable data. A ViewModel can retain screen-related state across configuration changes, but it is not durable storage and does not outlive process death by itself.

The system decides when an app process can be reclaimed. Apps should restore the user experience from explicit state and tolerate callbacks, cancellation, and re-creation.

## 7. Jetpack Compose UI

Compose is Android's modern declarative UI toolkit. A UI is described as composable functions that produce a tree from current state. When observed state changes, Compose schedules the relevant UI to update.

Core Compose ideas:

- Composables describe UI and should remain readable and focused.
- State should have a clear owner and flow to the UI.
- State hoisting moves state to the lowest common owner that needs to read or change it.
- Recomposition can call composables again; avoid relying on a composable body as a one-time event handler.
- Side effects belong in effect APIs with well-defined keys and lifetimes.
- Modifiers configure layout, interaction, drawing, and semantics.
- Previews help inspect UI configurations during development.

Compose can coexist with Views. Existing Views-based screens remain supported, and teams can migrate incrementally. Do not mix UI models without a clear interoperability boundary.

## 8. UI state and architecture

A user interface should represent application state rather than independently own business rules. A common flow is:

1. A user performs an action.
2. The UI sends an event to a state holder.
3. The state holder validates input and coordinates domain or data operations.
4. A repository provides or updates data.
5. Updated state flows back to the UI.
6. The UI renders loading, success, empty, and error states.

A ViewModel can hold screen state and coordinate lifecycle-aware work. A repository can isolate data sources such as a network API and local database. Domain-layer abstractions are useful when they simplify complex business rules, but unnecessary layers make a small app harder to understand.

## 9. Concurrency and background work

Coroutines provide structured concurrency for asynchronous Kotlin work. A coroutine has a scope, context, cancellation behavior, and lifecycle. Cancellation should propagate rather than be swallowed. Long-running operations should not block the main thread.

Flows represent asynchronous sequences of values. A UI should collect lifecycle-aware state when appropriate and stop work when it is no longer needed. Exceptions and retries should be handled at the layer that understands the operation.

Android restricts background execution to protect battery and user experience. Use WorkManager for deferrable work that should be scheduled with constraints and persisted across app restarts. Use foreground services only for allowed, ongoing work that is apparent to the user and complies with current system policy.

## 10. Local data and persistence

Choose storage based on data shape, lifetime, privacy, and query needs:

- Room provides a structured database layer over SQLite for relational local data.
- DataStore is suitable for preferences or small typed settings.
- Files are useful for larger documents and media.
- Cache storage holds data that can be recreated.
- Shared storage or a system picker is appropriate for selected user-visible files and media.

Database schema changes require migrations. Keep data access off the main thread. Define what happens when data is missing, stale, corrupt, or unavailable. Backups and device transfers are separate decisions from ordinary local persistence.

## 11. Networking

An Android app can use HTTP APIs through libraries or platform networking APIs. A networking layer should define request and response models, timeouts, serialization, authentication, error mapping, retries, and cancellation.

Network access is unreliable. The app should represent loading and failure, avoid blocking the UI, handle timeouts, and consider offline data where appropriate. Do not hard-code secret credentials in the app package. Use encrypted transport and validate server identity. Be careful when logging requests because they may contain personal data or tokens.

## 12. Permissions, privacy, and storage access

Permissions protect sensitive device capabilities and user data. Request a permission only when a feature needs it, explain the purpose in context, and handle denial or revocation. Platform permission rules can change by API level.

Use platform pickers and scoped access when possible instead of requesting broad access to user files. Store only the data the app needs and define retention behavior. For sensitive information, use suitable storage and encryption practices, and avoid exposing it in backups, logs, notifications, or screenshots.

Permission grants are not permanent authorization to collect data without limit. Respect the user's choice and provide useful behavior when optional access is denied.

## 13. Navigation, links, and inter-app actions

In-app navigation should preserve understandable back behavior and state. Deep links allow external URLs or intents to open relevant app content. Validate incoming URIs, restrict which components are exported, and avoid trusting unvalidated parameters.

Sharing can use Android's system chooser so users choose the destination app. Share only the data needed, use temporary URI grants where appropriate, and ensure that receiving apps can access the content.

## 14. Accessibility and adaptive UI

Accessible applications support screen readers, keyboard or switch navigation, clear focus, sufficient contrast, scalable text, and meaningful labels. Compose semantics expose UI structure to assistive technologies.

Android apps run on phones, tablets, foldables, TVs, watches, and other form factors. A responsive layout should use available space rather than assume one fixed screen. Consider window size classes, orientation changes, multi-window behavior, input methods, and posture changes where relevant.

## 15. Testing and debugging

Use a testing strategy that combines:

- Unit tests for logic and state transformations.
- Instrumented tests for behavior requiring an Android runtime.
- UI tests for user-visible flows and accessibility semantics.
- Manual checks for device-specific behavior and release configuration.

Logcat provides process and system logs. Debuggers inspect execution state. Android Studio profilers help investigate CPU, memory, network, and energy behavior. Avoid logging secrets or personal data, and use structured diagnostic context that can be removed or controlled in release builds.

Tests should be deterministic, clean up after themselves, and make assertions about observable behavior rather than fragile internal details.

## 16. Security

Android uses application isolation, permissions, component rules, and cryptographic APIs as layers of protection. App-level choices still matter:

- Keep exported entry points intentional and validate their inputs.
- Use HTTPS and platform network security controls.
- Keep API secrets and signing credentials outside public source code.
- Use Android Keystore for appropriate cryptographic key storage.
- Protect sensitive local data and backups according to its risk.
- Minimize permissions and data collection.
- Keep dependencies and build tools maintained.
- Prevent sensitive content from appearing in logs or screenshots.
- Validate inter-app intents, deep links, and file URIs.

Obfuscation is not a substitute for authorization or secret protection. A secret embedded in a distributed app can often be extracted.

## 17. Gradle and build variants

Gradle builds Android applications through tasks and plugins. The Android Gradle Plugin configures tasks and integrates Android-specific compilation and packaging. Build configuration has initialization, configuration, and execution phases.

Build types commonly distinguish debug and release behavior. Product flavors can represent product editions or environments. Build variants combine configured build types and flavors. Keep environment-specific endpoints and settings explicit, and do not put production secrets in source or variant files.

Dependencies are resolved from configured repositories. Pin or constrain versions deliberately, inspect transitive dependencies, and keep plugins compatible with the selected Gradle and SDK toolchain.

## 18. App artifacts, signing, and release

An APK is an Android installable package. An Android App Bundle is a publishing format from which distribution systems can generate device-specific APKs. The appropriate artifact depends on local testing, distribution channel, and current store requirements.

Signing establishes artifact identity and is part of update continuity. Protect signing keys, document recovery, limit access, and distinguish debug signing from release signing. Never commit private signing material.

A release process should cover versioning, build reproducibility, test results, signing, staged rollout, monitoring, rollback, privacy disclosures, and compatibility. Store policies and target API requirements change; verify them at release time.

## 19. Performance and battery

Keep expensive work off the main thread. Measure before optimizing. Startup time, frame rendering, allocations, memory retention, database queries, network usage, and wakeups can affect user experience and battery use.

Use lifecycle-aware work, batch operations when appropriate, cache carefully, and release resources when no longer needed. A background task that repeats too often or ignores constraints can harm battery life even if each individual operation is small.

## 20. Key mental models

- The Android system owns component and process lifetimes.
- UI is a representation of state, not a durable data store.
- Compose can recompose; one-time effects need explicit effect handling.
- A service does not automatically move work off the main thread.
- Permissions and exported components define important security boundaries.
- Build configuration changes which code and resources enter an artifact.
- App behavior can change with API level, device form factor, permission state, and distribution policy.
- The emulator is useful but does not reproduce every hardware or manufacturer behavior.
