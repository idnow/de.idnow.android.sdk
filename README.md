# Table of Contents

- [Overview](#overview)
  - [Requirements](#requirements)
  - [Android manifest](#android-manifest)
- [Integration](#integration)
  - [AAR library](#aar-library)
  - [Additional dependencies](#additional-dependencies)
  - [RenderScript](#renderscript)
- [Usage example](#usage-example)
- [Changelog](#changelog)

## Overview

The IDnow Android Archive Library (AAR file) can be used to incorporate the IDnow AutoIdent platform into Android host apps.

### Requirements

- minSdkVersion: 21 (Android 5 Lollipop)
- targetSdkVersion: it's recommended to specify the latest Android API level

### Android manifest

The following permissions and features are used by the IDnow library:

```
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<uses-feature android:name="android.hardware.camera" android:required="true" />
<uses-feature android:name="android.hardware.camera.autofocus" android:required="true" />
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
```

If you are using Android Studio, you don't need to specify the above in the host app's manifest. These are already present in the IDnow library manifest, and will be included during the build process according to these manifest merge rules: https://developer.android.com/studio/build/manifest-merge.html

## Integration

The recommended build tool is Gradle, so the examples below refer to build.gradle files.

### AAR library

Copy the idnow-platform-{version}.aar file into your app's libs folder (usually /app/libs). Whenever you see "{version}" used here, replace with the actual library version you want included, such as "3.12.0".

Add the "flatDir" block to the main (project-level) build.gradle, like this:

```
allprojects {
    repositories {
        ...

        flatDir {
            dirs 'libs'
        }
    }
}
```

Include these lines in the app module's build.gradle:

```
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation(name: 'idnow-platform-{version}', ext: 'aar')
    ...
}
```

### Additional dependencies

The IDnow framework depends on the presence of several other libraries. Please make sure to include the following list of dependencies into the app module's build.gradle file:

```
implementation 'com.android.support:recyclerview-v7:28.0.0'
implementation 'com.android.support:appcompat-v7:28.0.0'
implementation 'com.android.support.constraint:constraint-layout:1.1.3'

implementation 'com.squareup.okhttp3:okhttp:3.11.0'
implementation 'com.squareup.okhttp3:logging-interceptor:3.11.0'
implementation 'com.squareup.retrofit2:retrofit:2.4.0'
implementation 'com.squareup.retrofit2:converter-gson:2.4.0'

implementation 'org.msgpack:msgpack-core:0.8.16'
implementation 'org.msgpack:jackson-dataformat-msgpack:0.8.16'

implementation 'io.sentry:sentry-android:1.7.5'

api 'com.google.guava:guava:28.0-android'
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

## Usage example

```Java
public class IDnowAutoIdentActivity extends AppCompatActivity implements IDnowSDK.IDnowResultListener {

    private static IDnowSDK IDNOW_SDK = null;
    private static IDnowConfig IDNOW_CONFIG = null;
    private static String TAG = IDnowAutoIdentActivity.class.getSimpleName();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    
        IDNOW_SDK = IDnowSDK.getInstance();
        IDNOW_CONFIG = new IDnowConfig();
    
        IDNOW_SDK.initialize(this, IDNOW_CONFIG);
    }
    
    private void startIdent() {
        String identId = "TS3-XXXXX";
        IDNOW_SDK.startIdent(identId, this);
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

## Changelog

Only the most recent versions are included here. Please contact us if you are having trouble upgrading from an older version.

#### 3.15.1 - 28 Nov 2019
- improved user feedback for liveness detection

#### 3.15.0 - 25 Nov 2019
- user feedback in case the user is under age for the service
- improved user feedback if the ID card is expired
- minor bugfixes

#### 3.14.0 - 13 Nov 2019
- enhanced user experience in liveness detection and security feature steps
- user feedback for liveness detection
- camera focus fixes on specific devices

#### 3.13.0 - 18 Oct 2019
- improved user feedback if the ID document text is not read correctly
- improved user feedback if the person's photo in the document is not clearly visible
- customer configuration for the progress spinner color

#### 3.12.0 - 2 Oct 2019
- new scanning bar in the OCR, security features, selfie and liveness detection steps
- arrow style change in the liveness detection step
- consistent icons between VideoIdent and AutoIdent
- camera focus improvements
- customer configuration option to turn off the intro screen
- host app support for minifyEnabled (Proguard, R8)

#### 3.11.0 - 11 Sep 2019
- identification with any country's identity document
- resumable identification, even after the app has been put to the background
- configurable Finish screen
- bandwidth improvements for security features
- translation improvements, especially for Italian

#### 3.10.0 - 16 Aug 2019
- layout improvements in the intro, instructions, verification and T&C screens
- stability improvements
- minor bugfixes

#### 3.9.0 - 31 Jul 2019
- randomness in liveness detection
- new design for the T&C screen
- compliance with the Play Store's 64-bit requirement
- removed support for the x86 architecture

#### 3.8.0 - 17 Jun 2019
- pre-filled Ident ID when opening the app from a link
- customer configuration option for the progress circle color
- improvements related to the camera flashlight
- layout improvements in various screens
