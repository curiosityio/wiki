---
name: ngrok
---

# Install

* [Download](https://ngrok.com/download)
* Unzip. Install binary to your machine:

```
sudo mv ngrok /usr/local/bin/
```

* [Sign up or login](https://dashboard.ngrok.com/user/login)
* Authenticate to your account:

```
ngrok authtoken accountAuthTokenHere
```

# Usage

* Start your webserver locally. For example, if using Node, start up server: `npm start`
* Take note of the port your local webserver is listening on.
* Start ngrok on port local webserver listening on:

```
ngrok http 5000 # replace 5000 with port local webserver listening on
```
