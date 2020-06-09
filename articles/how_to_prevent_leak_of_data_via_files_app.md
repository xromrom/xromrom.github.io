# How to prevent leaking of data via Files application

## Impact
The Files app, introduced in iOS 11 has access to Documents folders of all applications. This feature can be useful if we want to share with user some file from application. Also, this feature can lead to leakage of sensitive data.

Files from app can be shared with iOS application "Documents" if two conditions are met:
* Two keys in ```Info.plist``` file – ```LSSupportsOpeningDocumentsInPlace``` and ```UIFileSharingEnabled``` have true value
* Files must be stored in application's Documents folder

## How to detect (white box)
If we have access to source code, we can open Xcode and check Info.plist for mentioned above keys.
If the keys are set - search in code for Documents folder identifiers.
For Swift this is ```documentDirectory```, for Objective-C – ```NSDocumentDirectory```.

## How to detect (black box)
First of all we need for keys ```LSSupportsOpeningDocumentsInPlace``` and ```UIFileSharingEnabled``` in ```Info.plist``` file.
To do this, let's ssh to our jailbroken device:
```shell
ssh root@192.168.X.Y
```

Then move go to tested application folder:
```shell
cd /var/containers/Bundle/Application/<APP_ID>/<APP_NAME.app>
```

Inside this folder you can find Info.plist file. We can't read it right now, because it's saved by default in binary format.
Let's do following:
1. Make copy of binary format:
```shell
cp Info.plist Info.plist_bin
```

2. Convert binary to xml format:
```shell
plutil -convert xml1 Info.plist
```

3. Now we can print plist file:
``` shell
cat Info.plist
```

Unfortunately, if we're doing black box review of app (aka penetration testing) we can't check folder that is used for saving of files. All we can (if we found these 2 keys in ```Info.plist```) - make some actions, that can invoke saving of files, then check Files app.

## Mitigations
Few options are exist:
* Turn off synchronisation with Files app by deleting keys ```LSSupportsOpeningDocumentsInPlace``` and ```UIFileSharingEnabled``` from ```Info.plist``` file.
* To hide data from user we can save files to other application folders: ```Library```, ```Library/Application Support Directory```. But we must be careful about saving to these folders – their content is persisted and included in the iCloud and iTunes backups.
