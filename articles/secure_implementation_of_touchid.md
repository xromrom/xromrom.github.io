# Secure implementation of TouchID

In this article we are going to review 2 different ways of using TouchID feature for authentication of user:
1. via LAContext - less secure way
2. and better approach - writing/reading from Keychain

First of all, thanks to David Lindner for [his great article](https://blog.nvisium.com/dont-touch-me-that-way) about TouchID. I totally recommend you
to check it. In my article I just tried to describe things shorter, plus I've updated
few things in code (since 2016 few flags became deprecated).

## Why solution with LAContext is less secure

Objective-C code for Local Authentication Context looks like this:
```objectivec
- (void)localAuthContextImplementation {
    LAContext *myContext = [[LAContext alloc] init];
    NSError *authError = nil;
    NSString *myLocalizedReasonString = @"For Securing App with TouchID";
    if ([myContext canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&authError]) {
        [myContext evaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics
                  localizedReason:myLocalizedReasonString
                            reply:^(BOOL success, NSError * _Nullable error) {

            if (success) {
                NSLog(@"User authenticated SUCCESSFULLY");
            } else {
                NSLog(@"User FAILED to authenticate successfully");
            }
        }];
    } else {
        // Could not evaluate policy
    }
}
```
(for Swift example - check [Apple's guide](https://developer.apple.com/documentation/localauthentication/logging_a_user_into_your_app_with_face_id_or_touch_id))

When method ```evaluatePolicy``` is called, user is asked for using his/her fingerprint to authenticate.
Still... why is it insecure? For 2 reasons:
1. Such code uses boolean variable ```success``` to decide whether user authenticated or not.
This can be easily bypassed with runtime manipulation tools like **cycript**.
2. Function ```evaluatePolicy``` just answers on the question - "is this fingerprint *exist at the moment* in iOS device settings?". So, if adversary gets access to the iOS device and adds his/her fingerprint, later this evil person can be successfully authenticated with function ```evaluatePolicy```.

That's not good, but luckily for us, there is another way.

## About SecItemAdd/SecItemCopyMatching implementation

Safe implementation may look like this (for Swift example - check [Apple's article](https://developer.apple.com/documentation/localauthentication/accessing_keychain_items_with_face_id_or_touch_id)):
```objectivec
- (void)secureImplementationInitialWrite {
    NSString *somePasswordToSave = @"S3cret_P@$$w0rd";
    CFErrorRef error = NULL;

    SecAccessControlRef sacObject = SecAccessControlCreateWithFlags(kCFAllocatorDefault,
                                                                    kSecAttrAccessibleWhenPasscodeSetThisDeviceOnly,
                                                                    kSecAccessControlBiometryCurrentSet,
                                                                    &error);

    NSDictionary *attributes = @{
        (__bridge id)kSecClass: (__bridge id)kSecClassGenericPassword,
        (__bridge id)kSecAttrService: @"TouchID_test",
        (__bridge id)kSecValueData: [somePasswordToSave dataUsingEncoding:NSUTF8StringEncoding],
        (__bridge id)kSecUseAuthenticationUI: @YES,
        (__bridge id)kSecAttrAccessControl: (__bridge_transfer id)sacObject
    };

    OSStatus initialWriteStatus = SecItemAdd((__bridge CFDictionaryRef)attributes, nil);

    if (initialWriteStatus == errSecSuccess) {
        NSLog(@"item was successfully saved");
    } else {
        NSLog(@"error while writing object to keychain!");
    }
}


- (void)secureImplementationReading {
    NSMutableDictionary *query = @{
        (__bridge id)kSecClass:(__bridge id)kSecClassGenericPassword,
        (__bridge id)kSecAttrService: @"TouchID_test",
        (__bridge id)kSecReturnData: @YES,
        (__bridge id)kSecUseOperationPrompt: @"Authenticate to get password!"
    }.mutableCopy;

    CFDataRef dataRef = NULL;
    OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&dataRef);

    if (status == errSecSuccess) {
        NSData *data = (__bridge NSData *)dataRef;
        NSString *string = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
        NSLog(@"String from secret storage: %@", string);
    } else {
        NSLog(@"Failed to read string from secret storage!");
        NSLog(@"status: %d", (int)status);
    }
}
```

First, check on mentioned above cycript analysis thing. With such evaluation request (method ```SecItemCopyMatching```) we can't tamper data because evaluation request and iOS Keychain data management are performed within the Secure enclave.

Second problem also fixed because for SecItemAdd we used flag ```kSecAccessControlTouchIDCurrentSet```. With such flag if current set of fingerprint changes, the SecItemCopyMatching evaluation fails.


## End
That's all I have to say, have a good day!
