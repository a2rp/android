# 15. Testing, debugging, and release

[Back to notes index](../README.md)

These notes cover ways to check Android app behavior, trace problems, and prepare a release build. The examples use Java, Android Views, Android Studio, and Gradle.

## 1. Test behavior at the right level

A local unit test runs on the computer's Java Virtual Machine. It is fast and fits logic that does not need a running Android device, such as formatting, validation, or calculations. Put these tests under `app/src/test/java/`.

An instrumented test runs on an emulator or physical device. It can use Android framework classes and inspect actual UI behavior. Put these tests under `app/src/androidTest/java/`. Use this level for behavior that depends on an Activity, Android views, a database, or interaction between app components.

Keep most checks small and focused. Add UI or end-to-end checks for important user paths, such as saving a note or recovering from an empty search. A test should state the expected behavior clearly and fail when that behavior changes unexpectedly.

## 2. Write a local Java unit test

For example, a small validator can reject a missing or blank note title without loading Android classes:

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

Test the behavior in `app/src/test/java/com/example/notes/NoteTitleValidatorTest.java`:

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

The test names describe the condition and expected result. Each test checks one behavior, which makes a failure easier to understand. Android Studio projects normally provide a JUnit dependency in the app module. If it is missing, add the JUnit version already used by the project to the app module's `testImplementation` dependencies.

Run local tests from the project root:

```powershell
.\gradlew.bat test
```

Gradle writes reports under the app module's `build/reports/tests/` folder. A green build means the tests passed, not that every possible behavior has been checked.

## 3. Check a screen with an instrumented UI test

Espresso can interact with visible Views and verify the result. Give important controls stable IDs in XML so a test can find them. For example, after a user taps `saveButton`, the app might show a confirmation message with the `noteSavedMessage` ID.

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

This example assumes the Activity starts with valid note content and displays the confirmation after saving. If saving needs input, fill the input fields in the test before tapping the button. A useful UI test checks what a user can observe, rather than reaching into private Activity fields.

Run instrumented tests with an emulator or a connected device:

```powershell
.\gradlew.bat connectedAndroidTest
```

Keep the emulator API level and device configuration relevant to the behavior being tested. A phone emulator does not replace checking a tablet layout, a different Android version, or a physical feature such as a camera.

## 4. Use Logcat to understand runtime behavior

Logcat displays messages from the app and Android system. Filter by the app process, tag, and severity to narrow down the output. Android's `Log` class provides levels such as `d` for debug, `i` for information, `w` for warning, and `e` for error.

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

Use a short, consistent tag so related messages are easy to filter. Do not write passwords, access tokens, private note contents, or other sensitive user data to logs. Remove noisy debug logging before release, or keep it behind a debug-only check.

When an app crashes, read the exception type and stack trace in Logcat. Start with the first stack-trace line that points to your app's code. The lines above it usually show the exception and message; the app frame shows where the failure occurred. Trace backward through the calls to find why the value or state was invalid.

## 5. Debug with breakpoints

Set a breakpoint on the line where the behavior first becomes incorrect, then run the app with **Debug**. When execution stops, inspect local variables and the call stack. Step over a line to continue in the current method, step into a method to follow its implementation, or step out to return to the caller. Add a watch for a value that changes over several steps.

Breakpoints are useful when a log message tells you that something went wrong but not why. An exception breakpoint can stop execution when an exception is thrown, including cases where app code catches it later. Remove or disable temporary breakpoints after fixing the issue so later debugging sessions behave as expected.

## 6. Check the release build separately

An app can work in a debug build and fail in a release build. Release builds may shrink resources, optimize code, use different server settings, and omit debug-only behavior. Test a release candidate before sharing it broadly.

R8 can shrink and optimize app code. Resource shrinking can remove unused resources. A Groovy Gradle build file can enable both in the release build type:

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

When a library uses reflection, serialization, or other indirect references, verify that R8 keeps the classes and members the app needs. Add a keep rule only for code that actually requires it. Install and test the minified release build because a successful debug build does not check shrinker behavior.

## 7. Sign and package a release

Android packages must be signed. For a Google Play release, an Android App Bundle (`.aab`) is the usual upload artifact. With Play App Signing, the upload key signs the bundle you upload, and Google Play manages the app signing key used for delivered APKs. Keep the upload key and its password private, back them up securely, and never commit them to a public repository.

In Android Studio, use **Build > Generate Signed Bundle / APK**, choose **Android App Bundle**, select or create the upload key, and build the release variant. Keep `versionCode` higher for each update and set the user-visible `versionName` appropriately. Install the generated build or distribute it through a private test track before a public release.

## 8. Release readiness checklist

Before sharing a release build, check the following:

1. Run local unit tests and instrumented tests for the changed behavior.
2. Open the app on a clean install and on an upgrade from the previous version when stored data matters.
3. Check important screens on narrow and wide windows, with larger text, and with accessibility services enabled.
4. Test denied permissions, offline behavior, empty states, and failure messages where those paths apply.
5. Build and install the release variant. Verify the app still works after code and resource shrinking.
6. Confirm the app label, icon, package ID, version values, and required manifest entries.
7. Confirm the signing key is available to the release owner and no key material or secrets are in the repository.
8. Review logs, remove test data, and verify that user information is not exposed in messages or screenshots.

## 9. Key points

- Use local JVM tests for logic that does not need Android, and device tests for Android behavior and UI.
- Test observable behavior with stable view IDs and clear expected results.
- Use Logcat and the debugger together: logs narrow the problem, and breakpoints reveal the state at the failing line.
- Treat the release build as a separate build to verify, especially when R8 and resource shrinking are enabled.
- Protect signing keys and increase `versionCode` for every published update.

## References

- [Test apps on Android](https://developer.android.com/training/testing)
- [Testing strategies](https://developer.android.com/training/testing/fundamentals/strategies)
- [Build local unit tests](https://developer.android.com/training/testing/local-tests)
- [Automate UI tests](https://developer.android.com/training/testing/ui-tests)
- [Run tests from the command line](https://developer.android.com/studio/test/command-line)
- [View logs with Logcat](https://developer.android.com/studio/debug/logcat)
- [Debug your app](https://developer.android.com/studio/debug)
- [Configure R8](https://developer.android.com/build/shrink-code)
- [Sign your app](https://developer.android.com/studio/publish/app-signing)
- [Upload your app bundle to Play Console](https://developer.android.com/studio/publish/upload-bundle)

---

[Previous: Accessibility and adaptive layouts](14-accessibility-and-adaptive-ui.md) | [Notes index](../README.md) | [Next: All code samples](98-all-code-samples.md)


