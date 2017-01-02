---
name: Testing
---

# Send mock SMS messages for testing SMS app

* Create Android emulator, start it up, install your app onto it, launch app.

* Find out the port the running emulator is on:

```
$> adb devices
List of devices attached
emulator-5554	device
```

Above example output says that the port is 5554.

* Get the emulator auth code in order to authenticate with the Android emulator in order to send SMS to it.

```
$> cat ~/.emulator_console_auth_token
s++Q3neNVXXd74WC
```

Copy the output from this command. In this example, copy `s++Q3neNVXXd74WC` to use later.

* Connect to the Android emulator using telnet:

```
$> telnet localhost 5554
```

Authenticate with Android emulator:

```
auth s++Q3neNVXXd74WC
```

You should see Android output saying success.

```
Android Console: type 'help' for a list of commands
```

* Send SMS to emulator from telnet:

```
sms send 555-555-5555 0
```
