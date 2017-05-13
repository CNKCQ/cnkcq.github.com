iOS10上传AppStore所遇到的坑

苹果的反馈邮件：
Dear developer,
We have discovered one or more issues with your recent delivery for "城满财富". To process your delivery, the following issues must be corrected:
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSContactsUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSCalendarsUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSBluetoothPeripheralUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSMicrophoneUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data.
This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSMotionUsageDescription key with a string value explaining to the user how the app uses this data.
Though you are not required to fix the following issues, we wanted to make you aware of them:
Missing Push Notification Entitlement - Your app includes an API for Apple's Push Notification service, but the aps-environment entitlement is missing from the app's signature. To resolve this, make sure your App ID is enabled for push notification in the Provisioning Portal. Then, sign your app with a distribution provisioning profile that includes the aps-environment entitlement. This will create the correct signature, and you can resubmit your app. See "Provisioning and Development" in the Local and Push Notification Programming Guide for more information. If your app does not use the Apple Push Notification service, no action is required. You may remove the API from future submissions to stop this warning. If you use a third-party framework, you may need to contact the developer for information on removing the API.
Once the required corrections have been made, you can then redeliver the corrected binary.
Regards,
The App Store team

看一下苹果的官方描述：
You must statically declare your app’s intended use of protected data classes by including the appropriate purpose string keys in your Info.plist file. For example, you must include the NSCalendarsUsageDescription key to access the user’s Calendar data. If you don’t include the relevant purpose string keys, your app exits when it tries to access the data.
解决方案：
```bash
<!-- 🖼 Photo Library -->
<key>NSPhotoLibraryUsageDescription</key>
<string>请点击“允许”以允许访问</string>

<!-- 📷 Camera -->
<key>NSCameraUsageDescription</key>
<string></string>

<!-- 🎤 Microphone -->
<key>NSMicrophoneUsageDescription</key>
<string></string>

<!-- 📍 Location -->
<key>NSLocationUsageDescription</key>
<string></string>

<!-- 📍 Location When In Use -->
<key>NSLocationWhenInUseUsageDescription</key>
<string></string>

<!-- 📍 Location Always -->
<key>NSLocationAlwaysUsageDescription</key>
<string></string>

<!-- 📆 Calendars -->
<key>NSCalendarsUsageDescription</key>
<string></string>

<!-- ⏰ Reminders -->
<key>NSRemindersUsageDescription</key>
<string></string>

<!-- 🏊 Motion -->
<key>NSMotionUsageDescription</key>
<string></string>

<!-- 💊 Health Update -->
<key>NSHealthUpdateUsageDescription</key>
<string></string>

<!-- 💊 Health Share -->
<key>NSHealthShareUsageDescription</key>
<string></string>

<!-- ᛒ🔵 Bluetooth Peripheral -->
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>

<!-- 🎵 Media Library -->
<key>NSAppleMusicUsageDescription</key>
<string></string>
```
<string></string> 之间的描述一定要填写
