---
name: AWS SNS push notifications
---

# Android

* First, follow [doc on android push notifications to get Android and GCM up and running](../android/push_notifications).

* Register your app with AWS SNS (if you have not created an app already in AWS SNS). *Tip: If you created a SNS app before but don't see it, make sure to select the region that you used to create the SNS app*

[Go to the SNS dashboard](https://console.aws.amazon.com/sns/) > Applications > Create platform application.
Application name: Your app name-Android
Push notifications service: GCM
API key: the server API key found [in your Google developer console API credentials](https://console.developers.google.com/apis/credentials).

# iOS

* First, follow [doc on iOS push notifications to get your iOS app and Apple developer account up and running](../ios/push_notifications).

* Register your app with AWS SNS (if you have not created an app already in AWS SNS). *Tip: If you created a SNS app before but don't see it, make sure to select the region that you used to create the SNS app*

* Continue reading on about p12 files.

### Choose p12 file

The p12 file is a certificate file. AWS is asking for the very specific certificate designated for sending push notifications. This is how we either (1) create one or (2) find the existing certificate and download it.

The certificate that AWS is looking for is a certificate generated specifically for an App ID that you create for your app and then enable push notifications for it.

**Check if you have already created this:**

* Go to your [Apple developer account](https://developer.apple.com/account/ios/identifier/bundle) > Identifiers > App ID. View the list of items here. If all you see is XC Wildcard then you did not create a cert and you need to continue to create one and you can skip ahead to the next part about creating a new certificate.

* If you see any other items in the list (besides XC Wildcard) select it then go to the bottom of the page and select "Edit".

* Scroll down to "Push notifications". If it is not selected via the checkbox on the left, check it to enable the feature. If you have added a certificate, you will see a "Download" button in this push notifications section. Download the certificate to your computer then go to the section below "Download and upload cert to AWS"

**Create new push notification certificate**

(this documentation taken from an old Parse document. I downloaded a copy of this documentation in this repo in the `misc_docs` root folder)

To begin, we'll need a certificate signing request file. This will be used to authenticate the creation of the SSL certificate.

* Launch the Keychain Access application on your Mac.

* Select the menu item Keychain Access > Certificate Assistant > Request a Certificate From a Certificate Authority…

* Enter your email address and name and that is it. Select "Saved to disk" to download the .certSigningRequest file to your desktop (or whatever you want. It is a temporary file anyway).

Now to create an App ID for your application:

* Every iOS application installed on your developer device needs an App ID. As a convention, these are represented by reversed addresses (ex. com.example.MyParsePushApp). For this push app, you can use an App ID you've already created, but make sure it does not contain a wildcard character (`*`).

* Navigate to the [Apple Developer account website](https://developer.apple.com/account/ios/identifier/bundle). Certificates > Identifiers & Profiles > Identifiers.

* You will see a list of your iOS App IDs. Select the + button in top right corner to register a new App Id.

* Enter a name for your new App ID, then make sure to select the checkbox next to Push Notifications under App Services. Name it "App name" such as "Awesome football app". Whatever name you want to use really. The name of your app. Scroll down and check the box for "Push notifications" to enable that feature.

* Select "Explicit App ID" and in the box enter your app's bundle ID. Click "Continue". Then on next screen "Submit" or "Done" whatever it is.

Now that you've created an App ID (or chosen an existing Explicit App ID), it's time to configure the App ID for Push Notifications.

* Select your newly created App ID from the list of iOS App IDs, then select "Settings".

* Scroll down to the Push Notifications section. Here you will be able to create both a Development SSL Certificate, as well as a Production SSL Certificate. Start by selecting "Create Certificate" under "Development SSL Certificate".

* The next screen explains how to create a certificate request. You already did this in this section when you downloaded it to your desktop. Select "Continue", then select "Choose File..." and locate the .certSigningRequest you created in your desktop.

* Select "Generate". Once the certificate is ready, select "Done" and download the generated SSL certificate from the "iOS App ID Settings" screen. Now proceed to the next section "Download and upload cert to AWS"

**Download and upload cert to AWS**

After you have downloaded a .cert file certificate for your App ID you created, it is time to upload this cert to AWS.

* Double click your .cert file you downloaded to open it in the Keychain Access program on your Mac. When the Keychain Access program opens, right click on it and "Export" it. Name it whatever you want and give it a password if you want to (don't have to. It is used to read the cert data when uploading to AWS). This will create a .p12 file that AWS requires and can read.

* Open the [AWS SNS applications](https://console.aws.amazon.com/sns/v2/home) page and select "Applications" on the left side > Create platform applications.

* Fill in this form:

Application name: Your app name-iOS (if adding a dev platform application, use app name-iOS-dev)
Platform: Apple development or Apple production. Whatever one you want to use for what purpose.
push certificate type: iOS push certificate (as we are doing iOS)
choose p12 file: Click "choose file" and select the p12 file you obtained above steps.
enter password: Enter password used when exporting your p12 certificate. Leave blank if you did not export with a password.
Certificate: leave blank. After you "choose p12 file" and enter your password above you click "Load credentials from file" and this field will populate.
Private key: save as "Certificate" above.

* Click "Load credentials" button and it should fill in the Certificate and Private key fields.

* click "create platform application".
