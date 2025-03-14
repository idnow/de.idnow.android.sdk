# Table of Contents

- [Overview](#overview)
  - [Requirements](#requirements)
  - [Compatibility, End of Support, End of Life](#compatibility-matrix)
  - [Android manifest](#android-manifest)
  - [Foreground Service Permission Explanation](#foreground_service_media_projection)
- [Integration](#integration)
  - [AAR library](#aar-library)
  - [RenderScript](#renderscript)
- [Usage example](#usage-example)
- [SDK error codes](#sdk-error-codes)
  - [How to deal with errors](#how-to-deal-with-errors)

## Overview

The IDnow Android Archive Library (AAR file) can be used to incorporate the IDnow AutoIdent platform into Android host apps.

Starting with version 4.9.0, we are providing the NFC capability to scan e-documents via 2 flavours of our IDnow library to provide the best flexibility for our customers.

- In case of interest in using the NFC capability, please reach out to IDnow to obtain the needed dependencies and further information on integration and usage.
- In case you would like to continue using our newest version of the library without NFC, please go through the rest of the documentation and follow the integration guide below.

### Compatibility Matrix
Please refer to the following link to find information about compatibility, end-of-support (EOS) and end-of-life (EOL) dates pertaining to our products: [IDnow Compatibility Matrix: Browser & OS Compatibility guide](https://www.idnow.io/developers/compatibility-overview/)

### Requirements

- AndroidX for IDnow library version 4.0.0 and beyond: https://developer.android.com/jetpack/androidx
- minSdkVersion: 24 (Android 7 Nougat)
- compileSdkVersion: 35 (Android 15)
- targetSdkVersion: 35 (Android 15)
- **not supported: devices and emulators based on the x86 architecture**

### Android manifest

The following permissions and features are used by the IDnow library:

```
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.NFC" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<!-- Android 13 Permissions -->
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<!-- Android 14 Permissions -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PROJECTION" />
<!-- Features -->
<uses-feature android:name="android.hardware.camera" android:required="true" />
<uses-feature android:name="android.hardware.camera.autofocus" android:required="false" />
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
<uses-feature android:name="android.hardware.nfc" android:required="false" />
```

If you are using Android Studio, you don't need to specify the above in the host app's manifest. These are already present in the IDnow library manifest, and will be included during the build process according to these manifest merge rules: https://developer.android.com/studio/build/manifest-merge.html

### FOREGROUND_SERVICE_MEDIA_PROJECTION
Starting with Android 14, we are required to add this special foreground service permission in our Manifest file, which applies automatically to all apps which include this SDK.

Since our SDK provides multiple functionalities based on customer requirements, a particular functionality used by some customers can record the device's screen to capture additional proof of the captured document.
But this functionality is being used only for customers who enabled this via customer configuration in order to be PVID compliant.

See [here a video recording](video/foreground_service_permission.mp4) where this permission is being requested at runtime.
In case Google Play Store asks you more information about this permission or proof that this is being requested, you can send them this video.

More info about understanding the foreground service media projection can be found [here](https://support.google.com/googleplay/android-developer/answer/13392821).

## Integration

AndroidX is needed starting with version 4.0.0 of our library. If you haven't migrated to AndroidX yet, please use an older library version, such as 3.20.1.

The recommended build tool is Gradle, so the examples below refer to build.gradle files.

### AAR library

In your project's build.gradle, besides whatever other repositories you already have, include this URL:

```
allprojects {
	repositories {
		...
		mavenCentral()
		google()
		maven {
		url "https://raw.githubusercontent.com/idnow/de.idnow.android.sdk/master"
		}
		...
	}
}
```

In the app module's build.gradle, besides whatever other dependencies you already have, include such an "implementation" line:

```
dependencies {
    implementation 'de.idnow.android.sdk:idnow-platform:5.13.0' // replace "5.13.0" with the version you want to include
}
```

#### NOTE: We also supply a special build of our AI SDK, which is a 1:1 copy of the official version, but it does not contain the FintecSystems SDK. This one can be integrated using the next line:
```
dependencies {
    implementation 'de.idnow.android.sdk:idnow-platform-no-fintec-XS2A:5.13.0' // replace "5.13.0" with the version you want to include
}
```

### RenderScript

Starting with v5.12.0, the `RenderScript` library has been removed. So, the below code is only required for version *5.11.0* or lower.

*Note:* When updating to the v5.12.0 version, make sure to remove the below code from your app's `build.gradle` file.

Additionally, the IDnow library needs `RenderScript` support for fast image processing. More info: https://developer.android.com/guide/topics/renderscript/compute

Please add these lines to the app module's build.gradle file:

```
android {
    ...

    defaultConfig {
        ...
        renderscriptTargetApi 24
        renderscriptSupportModeEnabled true
        vectorDrawables.useSupportLibrary = true
    }

    packagingOptions {
        pickFirst 'lib/armeabi-v7a/libRSSupport.so'
    }    
}
```

## Usage example

```Java
public class IDnowAutoIdentActivity extends AppCompatActivity implements IDnowSDK.IDnowResultListener {

    private IDnowSDK idnowSdk; 
    private static String TAG = IDnowAutoIdentActivity.class.getSimpleName();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        IDnowConfig idnowConfig = IDnowConfig.Builder.getInstance()
                .withLanguage("de") // this line is not necessary, please see below
                .build();

        idnowSdk = IDnowSDK.getInstance();
        idnowSdk.initialize(this, idnowConfig); 
    }
    
    // call this from somewhere, like a click listener
    private void startIdent() {
        String identId = "TS3-XXXXX";
        idnowSdk.startIdent(identId, this);
    }
    
    @Override
    public void onIdentResult(IDnowResult iDnowResult) {
        if (iDnowResult.getResultType() == IDnowResult.ResultType.FINISHED) {
            Log.d(TAG, "Finished");
        } else if (iDnowResult.getResultType() == IDnowResult.ResultType.CANCELLED) {
            Log.d(TAG, "Cancelled: " + iDnowResult.getStatusCode());
        } else if (iDnowResult.getResultType() == IDnowResult.ResultType.ERROR) {
            Log.d(TAG, "Error: " + iDnowResult.getStatusCode());
        }
    }
}
```

Using withLanguage("lang_code") you can configure the IDnow library to use a specific language. These ISO 639-1 language codes are currently supported: bg (Bulgarian), cs (Czech), da (Danish), de (German), el (Greek), en (English), es (Spanish), et (Estonian), fi (Finnish), fr (French), hr (Croatian), hu (Hungarian), it (Italian), ja (Japanese), ka (Georgian), ko (Korean), lt (Lithuanian), lv (Latvian), nb (Norwegian), nl (Dutch), pl (Polish), pt (Portuguese), ro (Romanian), ru (Russian), sk (Slovak), sl (Slovenian), sr (Serbian), sv (Swedish), tr (Turkish), zh (Chinese).

### Note:
Our recommended best practice for optimal user experience is to allow the SDK to use the device language instead of the lang_code.

### Language limitations
Some components that rely on 3rd party technologies such as Biometric Liveness or NFC scanning do not support the dynamic localization and use the device language.

If you are using an SDK version lower than 5.0.0, the returned result should be interpreted like this:

```Java
    public void onIdentResult(IDnowResult iDnowResult) {
        if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.FINISHED) {
            Log.d(TAG, "Finished");
        } else if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.CANCELLED) {
            Log.d(TAG, "Cancelled");
        } else if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.ERROR) {
            Log.d(TAG, "Error: " + iDnowResult.getMessage());
        }
    }
```

## SDK error codes

In case of IDnowResult.ResultType.ERROR, the IDnowResult.getStatusCode() method returns one of the error codes below.

```
"E100" --> Ident code syntax incorrect
"E101" --> Ident code not found
"E102" --> Ident code expired
"E103" --> Ident code already completed
"E110" --> Get ident info failed; invalid response
"E111" --> Get ident info failed; server reachability
"E130" --> Get ident resources failed; invalid response
"E131" --> Get ident resources failed; server reachability
"E140" --> Get name failed; invalid response
"E141" --> Get name failed; server reachability
"E142" --> Get name failed; full name missing
"E150" --> Start ident failed; invalid response
"E151" --> Start ident failed; server reachability
"E152" --> Start ident failed; missing session key
"E153" --> Start ident failed; wrong ident method
"E160" --> Get Emirates NFC resources failed; invalid response
"E161" --> Get Emirates NFC resources failed; server reachability
"E170" --> Socket connection force closed
"E171" --> Process force closed
"E180" --> Missing application context
```

### How to deal with errors

- For E102 it is recommended to create another ident, and restart the process with the new ident code.
- For E103 it is recommended to show a screen to the user with the message that they have submitted all info needed and that they should wait for the final result.
- For E170 it is recommended to notify the user that the ident process timed out or was started on a different device and ask them to try again.
- E180 is to alert the host app if the context has been lost (OS restarted/killed SDK process).
- For all other error codes it is recommended to show a generic error for the user and ask them to try again by restarting the process.