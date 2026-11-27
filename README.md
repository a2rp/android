# Android: Practical Learning Notes

These personal learning notes provide a structured path for building Android applications, from platform fundamentals and project setup to user interfaces, data, background work, security, testing, and release.

The course is Kotlin-first and focuses on Jetpack Compose for modern native UI. Android also supports Java and existing Views-based applications; chapters will identify where those approaches differ or remain relevant. Android platform behavior, SDK levels, libraries, Android Studio, and distribution requirements change over time, so version-sensitive guidance must state its assumptions.

## Learning outcomes

By following the roadmap, you will learn to:

- Explain Android app structure, application components, manifests, resources, and lifecycle behavior.
- Create and build an Android project with Android Studio, Kotlin, Gradle, and Android SDK tools.
- Build native interfaces with Compose, manage UI state, and support different screen sizes.
- Organize app logic with ViewModels, repositories, coroutines, flows, and persistent local data.
- Connect to network APIs and use background work while respecting platform execution limits.
- Handle permissions, app storage, privacy, notifications, and sensitive information safely.
- Test, debug, profile, sign, and prepare an application for release.
- Design for reliability, accessibility, performance, and maintainability across Android devices.

## Prerequisites

You should be comfortable using a computer and editing text files. Basic programming knowledge is helpful. The roadmap introduces Kotlin fundamentals before relying on Android-specific APIs. Familiarity with HTTP, JSON, databases, and Git will help in later chapters.

Hands-on work requires Android Studio and an Android SDK, plus either an emulator or a compatible device. Exact IDE, SDK, Gradle, and library versions should be selected according to the chapter's current requirements. Enable developer options only on a device you control. Keep signing keys and credentials out of source control.

## Quick guides

- [Android summary](android-summary.md)
- [Android keywords and concepts](android-keywords.md)

## How to use these notes

1. Follow the roadmap in order. It starts with Kotlin and project structure before moving into app components and UI.
2. Use the lesson to understand concepts, the Q&A to review decisions and edge cases, and the example to practice a complete task.
3. Use the emulator for most experiments. If you use a physical device, understand what data or settings an example may change.
4. Keep platform versions in mind. Permissions, background execution, storage, notification behavior, and release requirements can depend on API level and distribution policy.
5. Read the summary for a topic-wide review and use the keywords file as a terminology index.
6. Treat app signing credentials, API keys, private endpoints, and personal data as secrets. Do not commit them to this repository.

## Platform and UI scope

Jetpack Compose is the main UI approach in this roadmap. Android applications that already use Views remain supported, and Compose can coexist with Views during migration. The examples will explain relevant differences rather than mixing UI models without context.

Android is a managed application platform running on a Linux-based system. Apps interact with system capabilities through framework APIs and declared components. The operating system can stop app processes to reclaim resources, so important state must be stored deliberately and background work must follow current platform rules.

## Course roadmap

| No. | Lesson | Q&A | Example |
|---:|---|---|---|
| 001 | [Android platform architecture and development](lessons/001-android-platform-architecture-and-development.md) | [Open Q&A](qnas/001-android-platform-architecture-and-development.md) | [Open example](examples/001-android-platform-architecture-and-development.md) |
| 002 | [Android Studio project structure and Gradle](lessons/002-android-studio-project-structure-and-gradle.md) | [Open Q&A](qnas/002-android-studio-project-structure-and-gradle.md) | [Open example](examples/002-android-studio-project-structure-and-gradle.md) |
| 003 | [Kotlin foundations for Android](lessons/003-kotlin-foundations-for-android.md) | [Open Q&A](qnas/003-kotlin-foundations-for-android.md) | [Open example](examples/003-kotlin-foundations-for-android.md) |
| 004 | [Manifests, application components, and resources](lessons/004-manifests-application-components-and-resources.md) | [Open Q&A](qnas/004-manifests-application-components-and-resources.md) | [Open example](examples/004-manifests-application-components-and-resources.md) |
| 005 | [Activities, services, receivers, and content providers](lessons/005-activities-services-receivers-and-content-providers.md) | [Open Q&A](qnas/005-activities-services-receivers-and-content-providers.md) | [Open example](examples/005-activities-services-receivers-and-content-providers.md) |
| 006 | [Intents, navigation, and inter-app communication](lessons/006-intents-navigation-and-inter-app-communication.md) | [Open Q&A](qnas/006-intents-navigation-and-inter-app-communication.md) | [Open example](examples/006-intents-navigation-and-inter-app-communication.md) |
| 007 | [Activity lifecycle, processes, and state restoration](lessons/007-activity-lifecycle-processes-and-state-restoration.md) | [Open Q&A](qnas/007-activity-lifecycle-processes-and-state-restoration.md) | [Open example](examples/007-activity-lifecycle-processes-and-state-restoration.md) |
| 008 | [Jetpack Compose declarative UI foundations](lessons/008-jetpack-compose-declarative-ui-foundations.md) | [Open Q&A](qnas/008-jetpack-compose-declarative-ui-foundations.md) | [Open example](examples/008-jetpack-compose-declarative-ui-foundations.md) |
| 009 | [Compose state, recomposition, and side effects](lessons/009-compose-state-recomposition-and-side-effects.md) | [Open Q&A](qnas/009-compose-state-recomposition-and-side-effects.md) | [Open example](examples/009-compose-state-recomposition-and-side-effects.md) |
| 010 | [Compose layouts, Material Design, and theming](lessons/010-compose-layouts-material-design-and-theming.md) | [Open Q&A](qnas/010-compose-layouts-material-design-and-theming.md) | [Open example](examples/010-compose-layouts-material-design-and-theming.md) |
| 011 | [Navigation, accessibility, and adaptive layouts](lessons/011-navigation-accessibility-and-adaptive-layouts.md) | [Open Q&A](qnas/011-navigation-accessibility-and-adaptive-layouts.md) | [Open example](examples/011-navigation-accessibility-and-adaptive-layouts.md) |
| 012 | [App architecture, ViewModels, and UI state](lessons/012-app-architecture-viewmodels-and-ui-state.md) | [Open Q&A](qnas/012-app-architecture-viewmodels-and-ui-state.md) | [Open example](examples/012-app-architecture-viewmodels-and-ui-state.md) |
| 013 | [Coroutines, flows, and asynchronous work](lessons/013-coroutines-flows-and-asynchronous-work.md) | [Open Q&A](qnas/013-coroutines-flows-and-asynchronous-work.md) | [Open example](examples/013-coroutines-flows-and-asynchronous-work.md) |
| 014 | [Local data with Room, DataStore, and preferences](lessons/014-local-data-room-datastore-and-preferences.md) | [Open Q&A](qnas/014-local-data-room-datastore-and-preferences.md) | [Open example](examples/014-local-data-room-datastore-and-preferences.md) |
| 015 | [Networking, HTTP APIs, and serialization](lessons/015-networking-http-apis-and-serialization.md) | [Open Q&A](qnas/015-networking-http-apis-and-serialization.md) | [Open example](examples/015-networking-http-apis-and-serialization.md) |
| 016 | [Background work with WorkManager and constraints](lessons/016-background-work-workmanager-and-constraints.md) | [Open Q&A](qnas/016-background-work-workmanager-and-constraints.md) | [Open example](examples/016-background-work-workmanager-and-constraints.md) |
| 017 | [Services, notifications, and foreground work](lessons/017-services-notifications-and-foreground-work.md) | [Open Q&A](qnas/017-services-notifications-and-foreground-work.md) | [Open example](examples/017-services-notifications-and-foreground-work.md) |
| 018 | [Runtime permissions and privacy](lessons/018-runtime-permissions-and-privacy.md) | [Open Q&A](qnas/018-runtime-permissions-and-privacy.md) | [Open example](examples/018-runtime-permissions-and-privacy.md) |
| 019 | [App storage, files, and the Photo Picker](lessons/019-app-storage-files-and-photo-picker.md) | [Open Q&A](qnas/019-app-storage-files-and-photo-picker.md) | [Open example](examples/019-app-storage-files-and-photo-picker.md) |
| 020 | [Deep links, App Links, and sharing](lessons/020-deep-links-app-links-and-sharing.md) | [Open Q&A](qnas/020-deep-links-app-links-and-sharing.md) | [Open example](examples/020-deep-links-app-links-and-sharing.md) |
| 021 | [Resources, localization, and accessibility](lessons/021-resources-localization-and-accessibility.md) | [Open Q&A](qnas/021-resources-localization-and-accessibility.md) | [Open example](examples/021-resources-localization-and-accessibility.md) |
| 022 | [Android testing: unit, instrumented, and UI tests](lessons/022-android-testing-unit-instrumented-and-ui.md) | [Open Q&A](qnas/022-android-testing-unit-instrumented-and-ui.md) | [Open example](examples/022-android-testing-unit-instrumented-and-ui.md) |
| 023 | [Debugging with Logcat and Android Studio Profiler](lessons/023-debugging-logcat-and-android-studio-profiler.md) | [Open Q&A](qnas/023-debugging-logcat-and-android-studio-profiler.md) | [Open example](examples/023-debugging-logcat-and-android-studio-profiler.md) |
| 024 | [App security: secrets, encryption, and network security](lessons/024-app-security-secrets-encryption-and-network-security.md) | [Open Q&A](qnas/024-app-security-secrets-encryption-and-network-security.md) | [Open example](examples/024-app-security-secrets-encryption-and-network-security.md) |
| 025 | [Performance, memory, startup, and battery](lessons/025-performance-memory-startup-and-battery.md) | [Open Q&A](qnas/025-performance-memory-startup-and-battery.md) | [Open example](examples/025-performance-memory-startup-and-battery.md) |
| 026 | [Gradle dependencies, build types, and flavors](lessons/026-gradle-dependencies-build-types-and-flavors.md) | [Open Q&A](qnas/026-gradle-dependencies-build-types-and-flavors.md) | [Open example](examples/026-gradle-dependencies-build-types-and-flavors.md) |
| 027 | [App signing, APKs, and Android App Bundles](lessons/027-app-signing-apk-and-android-app-bundle.md) | [Open Q&A](qnas/027-app-signing-apk-and-android-app-bundle.md) | [Open example](examples/027-app-signing-apk-and-android-app-bundle.md) |
| 028 | [Release process, Play publication, and maintenance](lessons/028-release-process-play-publication-and-maintenance.md) | [Open Q&A](qnas/028-release-process-play-publication-and-maintenance.md) | [Open example](examples/028-release-process-play-publication-and-maintenance.md) |
| 029 | [Camera, location, sensors, and media](lessons/029-camera-location-sensors-and-media.md) | [Open Q&A](qnas/029-camera-location-sensors-and-media.md) | [Open example](examples/029-camera-location-sensors-and-media.md) |
| 030 | [Notifications, push messaging, and Firebase](lessons/030-notifications-push-messaging-and-firebase.md) | [Open Q&A](qnas/030-notifications-push-messaging-and-firebase.md) | [Open example](examples/030-notifications-push-messaging-and-firebase.md) |
| 031 | [Tablets, foldables, and multi-window experiences](lessons/031-tablets-foldables-and-multi-window-experiences.md) | [Open Q&A](qnas/031-tablets-foldables-and-multi-window-experiences.md) | [Open example](examples/031-tablets-foldables-and-multi-window-experiences.md) |
| 032 | [Capstone: build, release, and review an Android app](lessons/032-capstone-build-release-and-review-an-android-app.md) | [Open Q&A](qnas/032-capstone-build-release-and-review-an-android-app.md) | [Open example](examples/032-capstone-build-release-and-review-an-android-app.md) |

## Repository structure

- README.md - course introduction, setup guidance, and linked roadmap.
- LICENSE - MIT license for this notes repository.
- android-summary.md - topic-wide Android review and development concepts.
- android-keywords.md - Android APIs, platform terms, and concepts.
- lessons/ - chapter explanations, to be written in future steps.
- qnas/ - chapter questions and answers, to be written in future steps.
- examples/ - practical chapter examples, to be written in future steps.

## Future chapter content

The lesson, Q&A, and example files are intentionally empty in this initial scaffold. Their detailed content will be written in future steps, one chapter at a time.

## License

This repository is licensed under the MIT License. See [LICENSE](LICENSE).
