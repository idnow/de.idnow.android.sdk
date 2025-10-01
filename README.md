# Table of Contents

- [Overview](#overview)
  - [Requirements](#requirements)
  - [Compatibility, End of Support, End of Life](#compatibility-matrix)
  - [Android manifest](#android-manifest)
  - [Foreground Service Permission Explanation](#foreground_service_media_projection)
- [Integration](#integration)
  - [AAR library](#aar-library)
  - [RenderScript](#renderscript)
- [Language Support](#language-support)
- [Usage example](#usage-example)
  - [Binding key](#binding-key)
- [Integrate the NFC dependency](#integrate-the-nfc-dependency)
- [SDK error codes](#sdk-error-codes)
  - [How to deal with errors](#how-to-deal-with-errors)

## Overview

The IDnow Android Archive Library (AAR file) can be used to incorporate the IDnow AutoIdent platform into Android host apps.

Starting with version 4.9.0, the AutoIdent SDK supports NFC scanning to read data from the chip on e-documents. The AutoIdent SDK is available in 2 flavours, with NFC feature and without NFC, providing the best flexibility for different use cases of our customers. We recommend using the NFC feature for the best security, fraud prevention and user experience.

- To use the NFC feature, please reach out to your IDnow contact or IDnow Support (support@idnow.io) and obtain the required NFC dependencies. For information about the integration and usage, please refer to the section: [Integrate the NFC dependency](#integrate-the-nfc-dependency)
- In case you would like to continue using our newest version of the library without NFC, please go through the rest of the documentation and follow the integration guide below.

### Compatibility Matrix
Please refer to the following link to find information about current versions, compatibility, end-of-support (EOS) and end-of-life (EOL) dates pertaining to our products: [IDnow Compatibility Matrix: Browser & OS Compatibility guide](https://www.idnow.io/developers/compatibility-overview/)

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

When submitting your app for review on Google Play Store, please make sure to update your App declaration in the `Monitor and Improve → Policy and programs → App Content → Declarations → Foreground service permissions`.

The video recording proof of this permission being requested can be found here: [https://youtube.com/shorts/8IEhcrYRVRw](https://youtube.com/shorts/8IEhcrYRVRw)

The description of why this permission is included. Please use this description for submissions to Google Play:
```text
To comply with EU regulations, our app integrates a third-party SDK, IDnow AutoIdent, in order to verify user identities during the new user onboarding process. The ID verification process involves capturing and validating the user's ID document, the user's biometric selfie and establish that a live person is performing the ID verification. 

There are additional regulatory requirements in some countries which mandate recording a video of the Liveness step of the ID verification process to act as proof that a live person performed the ID verification. An example of such a regulation is PVID in France. It is mandatory for us to meet this requirement in order to be compliant with the regulations of specific countries, and to also prevent fraud. This permission is specifically requested during the Liveness Detection step in the identity verification flow. This step ensures the integrity of the ID verification process by capturing the user’s interaction during the liveness challenge, helping meet regulatory compliance in specific countries.

User consent is explicitly obtained twice in the flow - once in the beginning in the Terms & Conditions screen, and again before the Liveness detection step starts with a clear explanation of why the permission is being requested, as shown in the linked demonstration video. Users have full control to allow or deny this request and can stop the recording at any time by canceling the verification process using the "X" button. The recording is strictly limited to the duration of the Liveness Detection step within the verification flow and automatically stops once the user completes or exits this step.

Key points to note:
- Explicit user consent is required before recording starts
- Users can stop the recording at any time by canceling the verification flow via the "X" button
- The recording is limited to the liveness step of the ID verification process and automatically stops once the step is completed or exited
- No background recording occurs - this feature is only active while the user is actively engaged in the verification flow

```

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
    implementation 'de.idnow.android.sdk:idnow-platform:5.15.0' // replace "5.15.0" with the version you want to include
}
```

#### NOTE: We also supply a special build of our AI SDK, which is a 1:1 copy of the official version, but it does not contain the FintecSystems SDK. This one can be integrated using the next line:
```
dependencies {
    implementation 'de.idnow.android.sdk:idnow-platform-no-fintec-XS2A:5.15.0' // replace "5.15.0" with the version you want to include
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

## Language Support
Setting the withLanguage parameter (optional) tells the SDK the language in which the AutoIdent UI should be shown. If this parameter is not set, the SDK will first use the device language and if that is not available it falls back to English. Also, if the specified language or device language is not supported by our backend, then it will fall back to English. 

### Language limitations
3rd Party components such as Video Selfie and NFC scanning do not support the dynamic runtime language customization using the withLanguage parameter. These components always use the device language. **Hence, our recommended best practice for optimal user experience is to allow the SDK to use the device language instead of the preferredlanguage parameter.**

Using withLanguage("lang_code") you can configure the IDnow library to use a specific language. These ISO 639-1 language codes are currently supported: bg (Bulgarian), cs (Czech), da (Danish), de (German), el (Greek), en (English), es (Spanish), et (Estonian), fi (Finnish), fr (French), hr (Croatian), hu (Hungarian), it (Italian), ja (Japanese), ka (Georgian), ko (Korean), lt (Lithuanian), lv (Latvian), nb (Norwegian), nl (Dutch), pl (Polish), pt (Portuguese), ro (Romanian), ru (Russian), sk (Slovak), sl (Slovenian), sr (Serbian-Latin), sv (Swedish), tr (Turkish), zh (Chinese).

## Usage example

```Java
// Imports for the IDnow SDK
import de.idnow.core.IDnowConfig;
import de.idnow.core.IDnowResult;
import de.idnow.core.IDnowSDK;
import de.idnow.core.IDnowSDK.IDnowResultListener;


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


### Binding Key
BindingKey is a additional parameter that can be sent by customers when initializing the SDK. It can be used for device binding use cases and helps establish a correlation between a user's verified identity and their mobile device. It is particularly useful for device authentication and re-authentication scenarios when users change devices. BindingKey for a completed identification can be fetched via an API endpoint and compared with the one that was used during SDK initialization.

**Usage:**
```
//add this line after you initialized the IDnow SDK
IDnowSDK.getInstance().setBindingKey("YOUR_BINDING KEY")
```

### Integrate the NFC dependency
After contacting Customer Support with a request for the NFC package, you will receive an archive to integrate into your application.
This archive is compatible with the corresponding AI SDK version and onwards, until a new or updated version of the NFC library is released. (For example, if the name of the archive contains the keywords `for AI SDK v5.13.0`, it'd mean that this NFC library is compatible with AI SDK v5.13.0+ onwards, until an updated version of the NFC library is released).

**Note:** this NFC dependency is compatible with both our AI SDK flavours: `idnow-platform` and `idnow-platform-no-fintec-XS2A`

The contents of the received archive will contain:
- detailed instructions file on how to set-up the NFC dependency + fixes on potential build issues which you might encounter based on your local setup
- AAR files for NFC dependency to include in your app

Overview of the NFC integration
- copy all the `.aar` files from the given archive's `libs` folder into your own app's `libs` folder
- make sure to compile the `libs` folder. 
In your `app/build.gradle` file, add the below:
```groovy
implementation fileTree(include: ['*.aar'], dir: 'libs')
//or if you have also jar files, use this line
//implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')
```
In your project level `build.gradle` file, make sure to have this
```groovy
repositories {
  //...
  flatDir {
    dirs 'libs'
  }
  //...
}
```
- the above `aar` files require some transitive dependencies, which you need to add manually in your `app/build.gradle` file:
```groovy
    //ReadID Dependencies
    //iddoc connector
    implementation 'com.google.code.gson:gson:2.11.0'
    implementation 'androidx.annotation:annotation:1.5.0'
    implementation 'org.bouncycastle:bcpkix-jdk18on:1.78.1'
    //readid mrz
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.8.6'
    implementation ('com.google.android.gms:play-services-tflite-java:16.1.0') {
        exclude group: "org.tensorflow"
    }
    //readid core
    implementation 'org.jetbrains.kotlin:kotlin-stdlib:1.9.24'
    implementation 'net.sf.scuba:scuba-sc-android:0.0.26'
    implementation 'net.sf.scuba:scuba-smartcards:0.0.20'
    implementation 'edu.ucar:jj2000:5.2'
    implementation("org.ejbca.cvc:cert-cvc:1.4.13") {
        exclude group: 'org.bouncycastle', module: 'bcprov-jdk15on'
    }
    implementation 'org.jmrtd:jmrtd:0.7.42'
    //readid ui core
    implementation 'androidx.fragment:fragment:1.8.1'
    implementation 'androidx.preference:preference-ktx:1.2.1'
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.databinding:viewbinding:8.5.2'
    //viz capture
    implementation "com.google.mlkit:barcode-scanning:17.3.0"
    implementation "com.google.mlkit:face-detection:16.1.7"
    //end
```
- add the following proguard rules in your application's `proguard-rules.pro` file
```pro
# ReadID
-keep,includedescriptorclasses class com.readid.** { *; }
-keep enum nl.innovalor.logging.data.** { *; }
-keep enum nl.innovalor.logging.data.**$** { *; }
-keepclassmembers class nl.innovalor.logging.data.** { <fields>; }
-keep,includedescriptorclasses class net.sf.scuba.smartcards.IsoDepCardService { public <init>(***); }
-keep,includedescriptorclasses class nl.innovalor.cert.** { public <init>(***); }
-keep,includedescriptorclasses class org.bouncycastle.** { *; }
-keepclassmembers class * extends net.sf.scuba.data.Country { *; }
-keep,includedescriptorclasses class nl.innovalor.mrtd.model.** { private <fields>; <methods>; }
-dontwarn com.google.gson.**
-dontwarn javax.naming.**
-dontwarn javax.naming.directory.*
-dontwarn jj2000.disp.*
-dontwarn jj2000.j2k.decoder.*
-dontwarn org.jmrtd.imageio.*
-dontwarn org.jmrtd.jj2000.**
-dontwarn org.jnbis.imageio.*
```

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
