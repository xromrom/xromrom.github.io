# Check your Firebase Keys on excessive functionality

## Credits
All credits on this finding belong to abss, I recommend reading his article about Firebase Cloud Messaging Takeover ([link](https://abss.me/posts/fcm-takeover/#collaboration-and-poc-in-progress)).

## What is the issue?
Mobile apps often store Firebase/Google keys in their code to send Firebase analytics messages. But sometimes, the same key is used by the app to send push notifications, also called Firebase Cloud Messages (FCM). Leaking such a key with a mobile client can lead to mass phishing attacks by sending push notification messages.

You can easily find such a key because a Firebase key starts from "AIza..." and has a length of 35 chars.
You can check such key by the curl command:

```shell
curl --header "Authorization: key=AIzaSyAy4hsyC94oZC6WS_iu6_ZfkiEHalnwxGk" \
   --header Content-Type:"application/json" \
   https://fcm.googleapis.com/fcm/send \
   -d "{\"registration_ids\":[\"IID TOKEN A.K.A Registration token goes here\"]}"
```

If you receive a response with code 200 - this key can be used for sending push notifications.

## Mitigations
Always include FCM server keys within your app server logic and never in any client-end code. Also do not share these keys publicly on Github, Gitlab, Pastebin, and other similar online sources.
