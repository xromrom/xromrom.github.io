# Prevent leak of data via iCloud or iTunes backups

## Impact
Wrong configuration for saved files can lead to user sensitive data disclosure. User data can be stolen from:
* If adversary has access to user's Mac computer, data can be stolen from locally stored iTunes backups.
* If user Apple ID was compromised, application data can be stolen from iCloud.

Files from app are stored to iCloud/iTunes backups if two conditions are met:
1. Application saves file to one of its folders – Documents, Library, Application Support Directory
2. File saved without flag ```isExcludedFromBackup``` (or flag's value == ```false```).

## How to detect (black box)
1. Do things that invoke saving of file to disk (download and save file, for example)
2. Create iTunes backup (via iTunes, obviously)
3. Use some apps to extract files from backup (for example, iBackup Extractor for mac/windows)
4. Try to find files from tested app in backup files.

## How to detect (white box)
1. Check in code folders for saved to disk files:
  * For Swift: ```documentDirectory```, ```libraryDirectory```, ```applicationSupportDirectory```
  * For Objective-C: ```NSDocumentDirectory```, ```NSLibraryDirectory```, ```NSApplicationSupportDirectory```
2. Check if flag ```isExcludedFromBackup``` is applied to files from p.1

## Mitigations
To exclude sensitive files from backup we can use isExcludedFromBackup flag.
Swift code looks like this:
```swift
func excludeFileFromBackup(withURL url: URL) {
        var resourceUrl = url
        var resourceValue = URLResourceValues()
        resourceValue.isExcludedFromBackup = true
        do {
            try resourceUrl.setResourceValues(resourceValue)
        } catch { }
    }
```
