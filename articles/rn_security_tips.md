# ReactNative security tips

This article contains a few tips about making your RN apps more secure.
It may be useful for AppSec Analysts performing security assessments or for RN Developers trying to make their app more protected.

## 0. Avoid using of ReactNative, use native languages
Just kidding ;-)

## 1. Check dependencies on vulnerabilities
The open-source components that your app uses may have known vulnerabilities, to check them you can use the command:
```shell
npm audit
```
or:
```shell
yarn audit
```

## 2. Check that your app doesn't enable HTTP in production version
You need to review the files that may contain unsafe settings:
* Info.plist (for iOS app)
* AndroidManifest.xml (for Android app)

Disabled HTTPS in iOS looks like this (just remove the whole ```NSAppTransportSecurity``` tag to apply safe settings):

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

In Android such an unsafe setting looks like this (remove it from your manifest):
```xml
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">localhost</domain>
    </domain-config>
</network-security-config>
```

## 3. Disable development settings activity in AndroidManifest file
If your AndroidManifest.xml file contains code like this:
```xml
<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
```
it's better to remove such a string from a production build.

That's all, thank you for your attention!
