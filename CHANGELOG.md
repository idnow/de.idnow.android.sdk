## Changelog

#### 5.0.0 - 05 Oct 2022
- **BREAKING CHANGE: Added new ERROR codes for SDK callback**
- Removed SDK Application class so that the host app can define its own
- Updated introduction screen UI
- Enabled upload option on additional document step
- Updated Open banking dependency
- Various bug fixes

#### 4.19.1 - 04 Aug 2022
- Removed 3rd party dependency (Acra)

#### 4.19.0 - 03 Aug 2022
- Added new component for verifying user phone number via OTP
- Improved NFC and updated dependency. Please reach out to your CS manager to get the new NFC dependency
- Various bug fixes

#### 4.18.2 - 28 Jun 2022
- Various bug fixes

#### 4.18.1 - 10 Jun 2022
- Improved NFC and updated dependency. Please reach out to your CS manager to get the new NFC dependency
- Improved liveness detection step
- Improved reference bank transfer step
- Added the prefix “idnow_” to all local resources to avoid conflicts with similar resources in your app
- Various bug fixes

#### 4.16.1 - 21 Apr 2022
- Various performance improvements
- Various bug fixes
- Fix reported vulnerabilities due to 3rd party libraries

#### 4.16.0 - 31 Mar 2022
- Decreased minimum API version to 21
- Improved bank transfer
- Updated layout of the user feedback screens
- Various performance improvements
- Various bug fixes

#### 4.14.2 - 28 Jan 2022
- Various bug fixes

#### 4.14.1 - 24 Jan 2022
- Removed deprecated jCenter repository and switched to mavenCentral
- Various bug fixes

#### 4.14.0 - 11 Jan 2022
- Improved the document capture screen layout to improve focus
- Updated several dependencies for a better integration 
- Increased compileSdkVersion and targetSdkVersion to API 31 (Android 12)
- Updated Proguard rules

#### 4.13.0 - 26 Nov 2021
- Improved bank transfer and esigning
- Improved liveness
- Improved camera service
- Increased minimum API version to 23
- Fixed various bugs

#### 4.12.0 - 2 Oct 2021
- Bank Transfer feature
- Several improvements and optimizations
- Bug fixes

#### 4.11.2 - 29 Sep 2021
- New eSigning feature
- Enhanced support for customizing app animations
- Various bug fixes

#### 4.10.0 - 23 Jul 2021
- Support for 3 new languages: Arabic, Punjabi & Gujarati
- Enhancement of the feedback provided when scanning a document
- Various bug fixes

#### 4.9.0 - 29 Jun 2021
- E-Documents scanning via NFC (as optional feature), please contact IDnow for more info
- Enhancement of the feedback provided when scanning a document
- Various bug fixes

#### 4.8.1 - 3 May 2021
- Stability and performance improvements

#### 4.8.0 - 15 Apr 2021
- Enhancement of the feedback provided when scanning a document
- Various accessibility improvements
- Various bug fixes

#### 4.7.3 - 15 Mar 2021
- Bug fixes

#### 4.7.2 - 11 Mar 2021
- UI optimizations
- Stability and performance improvements
- Bug fixes

#### 4.7.0 - 21 Jan 2021
- User feedback improvements
- Stability and performance improvements
- Various bug fixes

#### 4.6.0 - 8 Dec 2020
- Improvements to manual picture taking process
- Improvements to the document scanning process
- Improved crash handling
- UI improvements and bug fixes
- Several bug fixes

#### 4.5.0 - 26 Oct 2020
- New branding
- UX improvements
- Various bug fixes

#### 4.4.0 - 13 Oct 2020
- Image quality enhancements
- Various bug fixes

#### 4.3.0 - 28 Sep 2020
- Additional customization options
- Updated translations
- Redesigned UX on some screens
- Various bug fixes

#### 4.2.1 - 31 Aug 2020
- Various bug fixes

#### 4.2.0 - 27 Aug 2020
- Option to manually take pictures of documents
- Option for users to specify a reason when cancelling
- New UX design for document selection
- User feedback improvements
- Language translation updates
- Various bug fixes

#### 4.1.0 - 2 Jul 2020
- Support for paper-based ID Cards and Residence Permits
- Various bug fixes

#### 4.0.0 - 29 Jun 2020
- Breaking change: Replaced the Support Library with AndroidX
- New liveness detection service
- Redesigned UI and UX
- Support for paper-based driver's license
- Various bug fixes

#### 3.20.1 - 22 Apr 2020
- Fonts are now configurable per customer
- Minor bug fixes and performance improvements

#### 3.20.0 - 20 Mar 2020
- Icons and animations are now configurable per customer
- Added possibility of manual selfie, if configured
- Integrated new liveness version
- Fixed crashes during selfie step
- Minor bug fixes and performance improvements

#### 3.19.0 - 2 Mar 2020
- Integration of new animations to improve overall look and feel, as well as user experience
- Improvement of security feature detection 
- Improvement of flows with multiple documents
- Multiple bug fixes

#### 3.18.0 - 17 Feb 2020
- Improvements in liveness detection
- Improvements in selfie step
- Visible Zone with MRZ check is also possible for one sided documents now
- Minor UI improvements and bugfixes

#### 3.17.0 - 23 Jan 2020
- Simplified the SDK integration by making the library dependencies private
- UI improvements and bug fixes

#### 3.16.0 - 19 Dec 2019
- Added support for language selection
- Removed camera autofocus requirement in the manifest
- Minor UI improvements and bugfixes

#### 3.15.1 - 28 Nov 2019
- Improved user feedback for liveness detection

#### 3.15.0 - 25 Nov 2019
- User feedback in case the user is under age for the service
- Improved user feedback if the ID card is expired
- Minor bugfixes

#### 3.14.0 - 13 Nov 2019
- Enhanced user experience in liveness detection and security feature steps
- User feedback for liveness detection
- Camera focus fixes on specific devices

#### 3.13.0 - 18 Oct 2019
- Improved user feedback if the ID document text is not read correctly
- Improved user feedback if the person's photo in the document is not clearly visible
- Customer configuration for the progress spinner color

#### 3.12.0 - 2 Oct 2019
- New scanning bar in the OCR, security features, selfie and liveness detection steps
- Arrow style change in the liveness detection step
- Consistent icons between VideoIdent and AutoIdent
- Camera focus improvements
- Customer configuration option to turn off the intro screen
- Host app support for minifyEnabled (Proguard, R8)

#### 3.11.0 - 11 Sep 2019
- Identification with any country's identity document
- Resumable identification, even after the app has been put to the background
- Configurable Finish screen
- Bandwidth improvements for security features
- Translation improvements, especially for Italian

#### 3.10.0 - 16 Aug 2019
- Layout improvements in the intro, instructions, verification and T&C screens
- Stability improvements
- Minor bugfixes

#### 3.9.0 - 31 Jul 2019
- Randomness in liveness detection
- New design for the T&C screen
- Compliance with the Play Store's 64-bit requirement
- Removed support for the x86 architecture

#### 3.8.0 - 17 Jun 2019
- Pre-filled Ident ID when opening the app from a link
- Customer configuration option for the progress circle color
- Improvements related to the camera flashlight
- Layout improvements in various screens
