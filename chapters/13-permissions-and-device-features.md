# 13. Permissions, privacy, and device features

[Back to notes index](../README.md)

| [Previous: Background work and notifications](12-background-work-and-notifications.md) | [Notes index](../README.md) | [Next: Accessibility and adaptive layouts](14-accessibility-and-adaptive-ui.md) |
|:--|:--:|--:|

## Ask for access only when a feature needs it

An Android permission protects access to a resource or user data. Declare only the permissions the app needs, and ask for runtime access when the user reaches the feature that needs it. A permission request should explain a clear action, such as taking a photo, rather than appear during general app startup.

| Permission type | How it is granted | Example |
| --- | --- | --- |
| Normal | Granted by the system when declared. | `INTERNET` for network access. |
| Runtime | Requested while the app is in use. The user can deny or later revoke it. | `CAMERA`, microphone, or location. |
| Special access | Usually enabled through a specific system settings page. | Some all-files or battery settings access. |

Declaring a permission and checking a device feature are related but separate. A permission gives access to a protected operation. A `<uses-feature>` declaration says whether the app needs a particular hardware or software capability to install or run.

## Declare an optional camera capability

If the camera is optional, mark the feature as optional so app stores do not exclude devices without a camera. The app can then check at runtime and keep its other features available:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature
        android:name="android.hardware.camera.any"
        android:required="false" />

    <application />

</manifest>
```

Before showing camera controls, ask `PackageManager` whether the capability exists:

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

If a hardware feature is essential to the app, set `android:required="true"` or omit the attribute, which defaults to required. If it is optional, check before using the hardware so the feature works correctly on different devices.

## Request a runtime permission in context

The usual permission flow has a few steps:

1. Check the permission just before the protected operation.
2. If it is missing, explain why when the user needs more context.
3. Request the permission with an Activity Result contract.
4. Handle both grant and denial.
5. Recheck before later operations because access can be revoked.

This Activity example requests camera access from the camera button. The rationale dialog should have a clear continue action and a cancel action that leaves the rest of the screen usable:

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

`showCameraRationale()` is a small UI method that explains why the camera button needs access and invokes its callback if the user chooses Continue. On a first request, Android may report that no rationale is needed. After a denial, the system may recommend showing one. Do not assume the exact number of prompts or the contents of permission groups.

The callback handles denial without crashing or blocking the rest of the app. If a feature remains unavailable, say what is affected. Avoid repeatedly opening system settings to pressure someone after they decline.

Android 11 (API 30) and higher can offer a one-time permission for location, camera, and microphone. The user can also revoke access in system settings. Check again when the feature is used and handle the protected operation failing.

## Prefer system selection when it avoids broad access

If the person only needs to attach one image, asking for access to the full media library is usually unnecessary. The Android Photo Picker lets them choose specific images or videos and grants access to those selected URIs. It does not require a broad media-library permission.

The Photo Picker contract in AndroidX Activity 1.7.0 or later can request one image:

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

The callback can receive `null` when the person closes the picker without selecting anything. If an upload will continue after the app or device restarts, persist access to the returned URI as needed with `ContentResolver.takePersistableUriPermission()`. Do not request access to every photo for a feature that only needs a selected file.

For taking a photo, the system camera app can also handle an image-capture intent. If the app directly controls the camera hardware through CameraX or Camera2, it needs the `CAMERA` runtime permission. A delegated system action can avoid requesting access the app itself does not use.

## Location should match the feature

Ask for foreground location only when a visible feature needs it. Coarse location may be enough for a nearby-city result. Use precise location only when the feature needs that accuracy. A user can grant approximate location when the app requests location access.

Background location is a separate and more sensitive need. Request it only when continuous access is essential to a core feature, explain why, and follow the separate system flow for the Android version and target SDK. If the feature can work while the activity is visible, avoid background access.

The same least-access approach applies to contacts, microphone, nearby devices, and files. A system picker or a single-purpose intent may provide the action without giving the app ongoing access to an entire data collection.

## Handle privacy choices in the app

- Request access only for the action the user chose. Do not collect extra data because it might be useful later.
- Explain which feature needs the permission and what data it uses.
- Keep the app usable when an optional permission is denied.
- Recheck access each time protected data or hardware is needed.
- Treat permission state as changeable. The user can revoke it, grant it once, or choose approximate location.
- Store only the information the feature needs, and use secure transport for data sent over a network.
- Check what permissions third-party libraries declare. A library's request appears to the user as the app's request.
- Test the screen with the feature granted, denied, revoked in settings, and unavailable on the device.

## Practice: optional photo attachment

Add an optional photo attachment to a study note. First check whether the device has a camera before showing the camera action. Use the Photo Picker for choosing an existing image, then handle cancellation and preview the returned URI. If the app also opens the camera hardware directly, request `CAMERA` only after the user taps that action. Test a device without a camera and test both granted and denied permission states.

## Notes to remember

- A permission protects an operation. A device feature check tells whether the hardware or capability exists.
- Normal permissions do not use a runtime prompt. Sensitive runtime permissions need a contextual request.
- Check access when using the feature, and handle revocation or denial.
- Use optional `<uses-feature>` declarations when hardware is not required for app installation.
- Use the Photo Picker for selected media instead of asking for access to the whole library.
- Request background location only when the core feature truly requires it.
- Give people a useful fallback when they deny an optional permission or lack the hardware.

## References

- [Request runtime permissions](https://developer.android.com/training/permissions/requesting)
- [App permissions best practices](https://developer.android.com/training/permissions/usage-notes)
- [Photo Picker](https://developer.android.com/training/data-storage/shared/photo-picker)
- [Access media files from shared storage](https://developer.android.com/training/data-storage/shared/media)
- [Background location](https://developer.android.com/develop/sensors-and-location/location/permissions/background)
- [Increase app availability across device types](https://developer.android.com/develop/adaptive-apps/guides/increase-availability)
- [PackageManager reference](https://developer.android.com/reference/android/content/pm/PackageManager)


