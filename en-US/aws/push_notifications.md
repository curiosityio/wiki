---
name: AWS SNS push notifications
---

# Android

* First, follow [doc on android push notifications to get Android and GCM up and running]('../android/push_notifications').

* Register your app with AWS SNS.

[Go to the SNS dashboard](https://console.aws.amazon.com/sns/) > Applications > Create platform application.
Application name: Your app name-Android
Push notifications service: GCM
API key: the server API key found [in your Google developer console API credentials](https://console.developers.google.com/apis/credentials). 
