# Table of Contents

- [Overview](#overview)
  - [Requirements](#requirements)
  - [Android manifest](#android-manifest)
- [Integration](#integration)
  - [AAR library](#aar-library)
  - [RenderScript](#renderscript)
  - [AndroidX core library](#androidx-core)
- [Usage example](#usage-example)

## Overview

The IDnow Android Archive Library (AAR file) can be used to incorporate the IDnow AutoIdent platform into Android host apps.

Starting with version 4.9.0, we are providing the NFC capability to scan e-documents via 2 flavours of our IDnow library to provide the best flexibility for our customers.

- In case of interest in using the NFC capability, please reach out to IDnow to obtain the needed dependencies and further information on integration and usage.
- In case you would like to continue using our newest version of the library without NFC, please go through the rest of the documentation and follow the integration guide below.

### Requirements

- AndroidX for IDnow library version 4.0.0 and beyond: https://developer.android.com/jetpack/androidx
- minSdkVersion: 23 (Android 6 Marshmallow)
- targetSdkVersion: it's recommended to specify the latest Android API level
- **not supported: devices and emulators based on the x86 architecture**

### Android manifest

The following permissions and features are used by the IDnow library:

```
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<uses-feature android:name="android.hardware.camera" android:required="true" />
<uses-feature android:name="android.hardware.camera.autofocus" android:required="false" />
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
```

If you are using Android Studio, you don't need to specify the above in the host app's manifest. These are already present in the IDnow library manifest, and will be included during the build process according to these manifest merge rules: https://developer.android.com/studio/build/manifest-merge.html

In the "application" element you also need to add the "extractNativeLibs" attribute, next to whatever other attributes you are using there.

```
<application
    android:extractNativeLibs="true"
    ...
```

## Integration

AndroidX is needed starting with version 4.0.0 of our library. If you haven't migrated to AndroidX yet, please use an older library version, such as 3.20.1.

The recommended build tool is Gradle, so the examples below refer to build.gradle files.

### AAR library

In your project's build.gradle, besides whatever other repositories you already have, include this URL:

```
allprojects {
    repositories {
        maven {
            url "https://raw.githubusercontent.com/idnow/de.idnow.android.sdk/master"
        }
    }
}
```

In the app module's build.gradle, besides whatever other dependencies you already have, include such an "implementation" line:

```
dependencies {
    implementation 'de.idnow.android.sdk:idnow-platform:4.13.0' // replace "4.13.0" with the version you want to include
}
```

### RenderScript

Additionally, the IDnow library needs `RenderScript` support for fast image processing. More info: https://developer.android.com/guide/topics/renderscript/compute

Please add these lines to the app module's build.gradle file:

```
android {
    ...

    defaultConfig {
        ...
        renderscriptTargetApi 16
        renderscriptSupportModeEnabled true
        vectorDrawables.useSupportLibrary = true
    }

    packagingOptions {
        pickFirst 'lib/armeabi-v7a/libRSSupport.so'
    }    
}
```

### AndroidX core library

Starting with version 4.14.0 of our library, we target API 31 (Android 12). It seems that the AndroidX Core library version 1.7.0 is incompatible with apps that target lower API levels. So, if your app does not specify at least 31 for compileSdkVersion and targetSdkVersion, please add these lines to the app module's build.gradle file:

```
configurations.all {
  resolutionStrategy {
    force 'androidx.core:core:1.6.0' // for Java projects
    force 'androidx.core:core-ktx:1.6.0' // for Kotlin projects
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
        if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.FINISHED) {
            Log.d(TAG, "Finished");
        } else if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.CANCELLED) {
            Log.d(TAG, "Cancelled");
        } else if (iDnowResult.getIDnowStatusCode() == IDnowResult.IDnowStatusCode.ERROR) {
            Log.d(TAG, "Error: " + iDnowResult.getMessage());
        }
    }
}
```

Using withLanguage("lang_code") you can configure the IDnow library to use a specific language. These ISO 639-1 language codes are currently supported: bg (Bulgarian), cs (Czech), da (Danish), de (German), el (Greek), en (English), es (Spanish), et (Estonian), fi (Finnish), fr (French), hr (Croatian), hu (Hungarian), it (Italian), ja (Japanese), ka (Georgian), ko (Korean), lt (Lithuanian), lv (Latvian), nb (Norwegian), nl (Dutch), pl (Polish), pt (Portuguese), ro (Romanian), ru (Russian), sk (Slovak), sl (Slovenian), sr (Serbian), sv (Swedish), tr (Turkish), zh (Chinese).
