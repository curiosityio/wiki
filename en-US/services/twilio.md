---
name: Twilio
---

# Get the text message sent to API endpoint from user.

In expressjs:

```
var messageSentFromUser = req.query.Body; // or req.body.Body if using POST endpoint
```

# Get phone number from user who just texted you.

```
var userPhoneNum = req.query.From; // or req.body.From if using POST endpoint
```

# Handle unsubscribe events from user

Twilio automatically handles subscribe and unsubscribing of texts from your users (of 10 digit phone numbers. If you want to handle it yourself, use Short codes). When a user texts stop, quit, cancel, unsubscribe to your number, Twilio automatically handles this and prevents you from sending texts to this phone again. You will receive a HTTP error the next time that you try to send a text.

Users however have the option to resubscribe to you (again handled by Twilio) by texting "start" to your phone number. This would make it a good idea for when you create text message services to have users say "start" to your phone number to get them started receiving texts from your number in case they previously unsubscribed.

[Official doc in FAQ covering this](https://www.twilio.com/help/faq/sms/can-you-customize-the-helpstop-messages-for-sms-filtering)

# I created an endpoint for my application. Now what?

* Setup a webserver to host the application. DigitalOcean, Heroku, or ngrok (I recommend ngrok for testing purposes).
* Create a Twilio account. Obtain a phone number that can use the services you need for your application (SMS, voice, MMS).
* Go to the [phone numbers dashboard](https://www.twilio.com/console/phone-numbers/dashboard) after your get a phone number and select your phone number you want your application to work with.
* Enter in details for phone number.

![](/docs/images/twilio_config_phone_num.png)
