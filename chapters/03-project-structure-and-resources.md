# 3. Project structure, Gradle, and resources

[Back to notes index](../README.md)

| [Previous: Java essentials for Android](02-java-for-android.md) | [Notes index](../README.md) | [Next: Activities and the Android lifecycle](04-activities-and-lifecycle.md) |
|:--|:--:|--:|

## How an Android project is arranged

An Android Studio project is the whole build. It can contain one or more modules. A module is a part that Gradle builds, such as an installable app or a reusable library. In a simple project, the app module is usually named `app`.

The project view may show a compact Android-specific layout, while the Project view shows the actual folders. The same files are present even when the IDE groups them differently.

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

Some projects use `build.gradle.kts` and `settings.gradle.kts` file names. Keep the generated file format when editing a project. Do not copy syntax from a different format into the build file.

## What the main files do

| File or folder | Purpose |
| --- | --- |
| `settings.gradle` | Names the build and includes its modules. |
| Root `build.gradle` | Declares build plugins and settings shared across modules. |
| `gradle/wrapper/gradle-wrapper.properties` | Selects the Gradle distribution used by the wrapper. |
| `gradlew` and `gradlew.bat` | Run the project's selected Gradle version on Unix-like systems and Windows. |
| `app/build.gradle` | Configures the app module, SDK levels, application ID, build types, and dependencies. |
| `app/src/main/AndroidManifest.xml` | Declares app components, permissions, and other package information. |
| `app/src/main/java/` | Holds Java source files for the main app. |
| `app/src/main/res/` | Holds compiled Android resources such as layouts, strings, images, and themes. |
| `app/src/test/` | Holds local tests that run on the development machine. |
| `app/src/androidTest/` | Holds tests that need an Android device or emulator. |

The app module has its own build file because it can be configured and built separately from other modules. Dependency declarations also belong to the module that uses them.

## Gradle and the Android build plugin

Gradle is the build system. The Android Gradle Plugin adds Android-specific tasks, such as compiling resources, packaging an app, and creating build variants. Android Studio sync reads the build files and checks that the project and its dependencies can be configured. When I choose **Run**, Android Studio asks Gradle to build the selected variant before installing it on a device.

The Gradle wrapper scripts let everyone use the Gradle version selected by the project. On Windows, a command can be run from the project root with the wrapper:

```text
gradlew.bat :app:tasks
```

This lists tasks for the `app` module. To build its debug APK, run:

```text
gradlew.bat :app:assembleDebug
```

The wrapper may need to download its configured Gradle distribution the first time it runs. Keep the wrapper files in version control so another checkout can use the same build setup.

## SDK levels and app identity

The app module build file contains settings with different jobs. They may have similar numbers, but they are not interchangeable.

| Setting | What it controls |
| --- | --- |
| `namespace` | The package used for generated resource classes such as `R`. It should normally match the base package used by app code. |
| `applicationId` | The unique identity installed on a device and used by app stores. Keep it stable after publishing an app. |
| `compileSdk` | The Android API definitions available while compiling the source. It does not decide the minimum device version. |
| `minSdk` | The lowest Android API level allowed to install the app. |
| `targetSdk` | The Android behavior level the app opts into and has been tested against. Platform behavior and store requirements can depend on it. |

For a new project, the namespace and application ID usually start with the same value. They serve different purposes, so changing one does not always mean the other should change. Use the SDK levels selected for the project and check current platform requirements before publishing.

## Resource folders and names

Resources are files that describe or provide content for the app. Keeping them outside Java source makes them easier to reuse and lets Android select alternatives for a device configuration, such as a language or screen density.

| Resource folder | Common contents | Example reference |
| --- | --- | --- |
| `res/layout/` | XML view layouts | `R.layout.activity_main`, `@layout/activity_main` |
| `res/values/` | Strings, colors, dimensions, styles, and themes | `R.string.app_name`, `@string/app_name` |
| `res/drawable/` | Images and XML drawable definitions | `R.drawable.logo`, `@drawable/logo` |
| `res/mipmap/` | Launcher icon resources | `R.mipmap.ic_launcher` |
| `res/menu/` | Menu definitions | `R.menu.main_menu` |
| `res/raw/` | Files opened as raw resources | `R.raw.sample_data` |

Resource directory and file names use lowercase letters, digits, and underscores. For example, `welcome_message.xml` is valid, while `WelcomeMessage.xml` is not a valid resource file name. Values resource XML files can contain several named values; layout and drawable resource names usually come from their file names.

## Use a string resource

Create or update `app/src/main/res/values/strings.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">StudyApp</string>
    <string name="welcome_message">Welcome to my study notes</string>
</resources>
```

Use the resource from XML with `@string/name`:

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/welcome_message" />
```

Use it from Java with the generated resource ID:

```java
titleView.setText(R.string.welcome_message);
```

Android build tools generate the `R` class from resources. `R.string.welcome_message` is a Java resource ID, while `@string/welcome_message` is the XML reference to the same resource. Never edit a generated `R` file. Fix the source resource or its name, then build again.

## Build variants and generated files

Android projects normally define debug and release build types. Debug builds are intended for local development. Release builds are configured for distribution and may have different signing and optimization settings. Build types and product flavors can combine into variants; release details are covered in the final development chapter.

Generated files and build outputs are not source files. Android Studio and Gradle recreate them, so do not manually edit files inside `build/` or generated source directories. Make changes in `src/main`, resource folders, the manifest, or the module build file.

## Common build and resource problems

| Symptom | What to check |
| --- | --- |
| Gradle sync fails | Read the first reported error, check network access for required dependencies, and confirm the Gradle wrapper and Android Gradle Plugin versions are compatible. |
| A resource reference is red | Check the resource file name, resource type, XML syntax, and whether the file is under the app module's `res/` folder. |
| `R` cannot be resolved | Fix resource and manifest errors first, then sync or build again. Do not create an `R` class yourself. |
| The wrong app is installed | Check `applicationId`, since it identifies the installed app. |
| Java code imports the wrong `R` | Check the module `namespace`. Import that generated `R` class when the Java class is in a different package. |
| A resource works on one device but not another | Check configuration-specific resource folders and provide a default resource when one is needed. |

## Practice: change the app label

1. Find the app module's `namespace` and `applicationId` in its build file.
2. Open `res/values/strings.xml` and change the `app_name` value.
3. Confirm that the manifest's application label refers to `@string/app_name`.
4. Build and run the app, then check the name shown in the launcher and recent-apps screen.
5. Add a new layout string and reference it once from XML and once from Java.

## Notes to remember

- The project can contain multiple modules, and each module has its own build configuration.
- The wrapper scripts run the Gradle version selected by the project.
- `namespace`, `applicationId`, `compileSdk`, `minSdk`, and `targetSdk` have separate purposes.
- Resource names become generated IDs in `R`.
- Keep user-visible text in string resources instead of hardcoding it in every layout.
- Edit source files and resources, not generated output.

## References

- [Android build structure](https://developer.android.com/build/android-build-structure)
- [Configure your build](https://developer.android.com/build)
- [Configure the app module](https://developer.android.com/build/configure-app-module)
- [App resources overview for Views](https://developer.android.com/topic/architecture/views/resources/providing-resources-views)
- [Layout resources](https://developer.android.com/guide/topics/resources/layout-resource)
- [String resources](https://developer.android.com/guide/topics/resources/string-resource)

