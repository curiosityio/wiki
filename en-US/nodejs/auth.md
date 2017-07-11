---
name: Authentication
---

# Passportjs

[Passportjs](http://passportjs.org/) is an outstanding library for authentication in your node app. It is a framework that allows you to plugin auth for many different services such as OAuth2, Facebook, Twitter, LinkedIn, Google, etc. You can write your own custom login as well for your own services!

# Google login

If you want to allow users to login to your web app via Google, this is how you would go about doing so.

We will be using Google's official node library instead of passportjs for this task. I attempted to use Passportjs but was not successful with the fact that Passportjs uses sessions to keep users logged into your application. I wanted to use OAuth2 Bearer access tokens with my own application so that did not work out for me.

```
User presses Login with Google button on website. Uses Google JS library to start the flow...
        |
        |
User directed to Google website to login or skip this if already logged in on browser.
        |
        |
User directed to Google website to allow/deny access to your application.
        |
        |
Callback function called back in your web app. Callback function given one-time use access code from Google you can exchange for access token.
        |
        |
Web app sends access code from Google up to *your own* node server.
        |
        |
Use exchange the Google access code for a Google access token on your server.
        |
        |
Create a new user or query existing one in your database. The returned User Google gives you back has a unique ID you can use to create users with.
        |
        |
Create access token for user account. Save to database for user. Return back access token to user.
```

Lets get to it. I am following [this guide from Google on "Google Sign-In for server-side apps"](https://developers.google.com/identity/sign-in/web/server-side-flow) to do this.

* Create new Google application in the [Google developer console](https://console.developers.google.com/project/_/apiui/apis/library). Select existing project you already created for this app you are creating (perhaps you already implemented Google Maps or something?) or if you have not created a project yet, create a new one.

Go to API Manager > Credentials > OAuth consent screen. Fill in the fields that it asks for here such as name. Now go to Credentials > New Credentials > OAuth client ID > Web application.

This just created your client ID that you need for the JS code in your web app.

We need to enter in "Authorized JavaScript origins". You can enter however many you need. Development and production origins:

```
http://localhost:8080
https://myproductionurl.example.com
```

"Authorized redirect URI" can be left blank. We want to be redirected back to a function in our web app, not your server as there is no way to get called back from your web server to your web app.

* Time to add some code to your web app:

```
<!-- The top of file index.html -->
<html itemscope itemtype="http://schema.org/Article">
<head>
  <script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js"></script>
  <script src="https://apis.google.com/js/client:platform.js?onload=start" async defer></script>
  <script>
    function start() {
      gapi.load('auth2', function() {
        auth2 = gapi.auth2.init({
          client_id: 'YOUR_CLIENT_ID.apps.googleusercontent.com',
          // Scopes to request in addition to 'profile' and 'email'
          //scope: 'additional_scope'
        });
      });
    }
  </script>
</head>
<body>
  <!-- ... -->
</body>
</html>
```

Add the following code to your `<head>` while also replacing `YOUR_CLIENT_ID` with well, your client id that you obtained in the step above.

* Add the Login with Google button to your site:

```
<!-- Add where you want your sign-in button to render -->
<!-- Use an image that follows the branding guidelines in a real app -->
<button id="signinButton">Sign in with Google</button>
<script>
  $('#signinButton').click(function() {
    // signInCallback is defined in the next step
    auth2.grantOfflineAccess({'redirect_uri': 'postmessage'}).then(signInCallback);
  });
</script>
```

`grantOfflineAccess` means that your server is able to obtain an access token and call the Google API on your behalf. This does not mean your web app is going to go offline, just means that while you are offline, a web server can make calls using your account.

We use `postmessage` as the `redirect_uri` because we want `signInCallback` to be called. Not a URL.

* Now our `signInCallback` function:

```
<!-- Last part of BODY element in file index.html -->
<script>
function signInCallback(authResult) {
  if (authResult['code']) {
    // Hide the sign-in button now that the user is authorized, for example:
    $('#signinButton').attr('style', 'display: none');
    // Send the code to your server
    $.ajax({
      type: 'POST',
      url: 'http://example.com/storeauthcode',
      contentType: 'application/octet-stream; charset=utf-8',
      success: function(result) {
        // Finally, you're signed in successfully!
        // save result.access_token (or whatever you call it on your web server response object) to local storage, go to your user signed in only web page.
      },
      processData: false,
      data: authResult['code']
    });
  } else {
    // There was an error.
  }
}
</script>
```

Here, `signInCallback` is taking in `authResult` which is an object returned back from Google. We are taking the property `code` from authResult: `authResult['code']` and sending that up to your API via a POST call to `http://example.com/storeauthcode`. We will be defining out API endpoint next.

This is all you need to do on the web app side. Next lets go to the node web server part.

* Exchanging the Google auth code for an access token.

I am using [Expressjs](http://expressjs.com/) for the API. Here is the endpoint:

```
var router = require('express').Router();
var google = require('googleapis');
var googleOAuth2 = google.auth.OAuth2;
var googlePlus = google.plus('v1');

var GOOGLE_CLIENT_ID = 'YOUR_CLIENT_ID.apps.googleusercontent.com';
var GOOGLE_CLIENT_SECRET = 'YOUR_CLIENT_SECRET';

router.post('/user/login/google/callback', function(req, res, next) {
    if (!req.body.code) {
        return res.status(401).send({error: 'You did not send your access code!'});
    }

    var googleOAuth2Client = new googleOAuth2(GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, config.getCounselorGoogleLoginCallback());
    googleOAuth2Client.getToken(req.body.code, function(err, tokens) {
        if (err) return next(new Error(err));
        // we have gotten tokens from Google. I don't save them, as I don't need to. I simply need an email address from this user and an image. That is it.         
        googleOAuth2Client.setCredentials(tokens);
        googlePlus.people.get({userId: 'me', auth: googleOAuth2Client}, function(err, google_user) {
            if (err) return next(new Error(err));

            // we have received the User from Google with all the info we need.
            var usersGoogleEmail = google_user.emails[0].value;
            var googleId = google_user.id;
            // Now, you can query your database for an existing user with this email/ID or create a new one if does not exist.
            // Create a new access token for user, and send back:
            return res.status(200).send({status: 'success', access_token: user.access_token});        
        });
    });
});
```

Here is an example response from Google with all the user info:

```
{
   "id":"173726013240634421249",
   "displayName":"Foo Stewart",
   "name":{
      "familyName":"Stewart",
      "givenName":"Foo"
   },
   "emails":[
      {
         "value":"foo.stewart@gmail.com",
         "type":"account"
      }
   ],
   "photos":[
      {
         "value":"https://lh4.googleusercontent.com/-yXOIeF2pO-4/AAAAAAAAAAI/AAAAAAAAAGw/wNy61-k63cw/photo.jpg?sz=50"
      }
   ],
   "gender":"male",
   "provider":"google",
   "_raw":"{\n \"kind\": \"plus#person\",\n \"etag\": \"\\\"4OZ_Kt6ujOh1jaML_U6RV6APqoE/URALIYi7eHsyj829e3qMjFLO18U\\\"\",\n \"gender\": \"male\",\n \"emails\": [\n  {\n   \"value\": \"foo.stewart@gmail.com\",\n   \"type\": \"account\"\n  }\n ],\n \"objectType\": \"person\",\n \"id\": \"173726013240634421249\",\n \"displayName\": \"Foo Stewart\",\n \"name\": {\n  \"familyName\": \"Stewart\",\n  \"givenName\": \"Foo\"\n },\n \"url\": \"https://plus.google.com/173726013240634421249\",\n \"image\": {\n  \"url\": \"https://lh4.googleusercontent.com/-yXOIeF2pO-4/AAAAAAAAAAI/AAAAAAAAAGw/wNy61-k63cw/photo.jpg?sz=50\",\n  \"isDefault\": false\n },\n \"isPlusUser\": true,\n \"language\": \"en\",\n \"circledByCount\": 29,\n \"verified\": false,\n \"cover\": {\n  \"layout\": \"banner\",\n  \"coverPhoto\": {\n   \"url\": \"https://lh3.googleusercontent.com/QgxqKoL0_5SZvxtytTtcrX_5nqbyux7HFwTWCAht9z0LBq_i69_R3k07cKxOxW0YNQoL=s630-fcrop64=1,00000086ffffff78\",\n   \"height\": 531,\n   \"width\": 940\n  },\n  \"coverInfo\": {\n   \"topImageOffset\": 0,\n   \"leftImageOffset\": 0\n  }\n }\n}\n",
   "_json":{
      "kind":"plus#person",
      "etag":"\"4OZ_Kt6ujOh1jaML_U6RV6APqoE/URALIYi7eHsyj829e3qMjFLO18U\"",
      "gender":"male",
      "emails":[
         {
            "value":"foo.stewart@gmail.com",
            "type":"account"
         }
      ],
      "objectType":"person",
      "id":"173726013240634421249",
      "displayName":"Foo Stewart",
      "name":{
         "familyName":"Stewart",
         "givenName":"Foo"
      },
      "url":"https://plus.google.com/173726013240634421249",
      "image":{
         "url":"https://lh4.googleusercontent.com/-yXOIeF2pO-4/AAAAAAAAAAI/AAAAAAAAAGw/wNy61-k63cw/photo.jpg?sz=50",
         "isDefault":false
      },
      "isPlusUser":true,
      "language":"en",
      "circledByCount":29,
      "verified":false,
      "cover":{
         "layout":"banner",
         "coverPhoto":{
            "url":"https://lh3.googleusercontent.com/QgxqKoL0_5SZvxtytTtcrX_5nqbyux7HFwTWCAht9z0LBq_i69_R3k07cKxOxW0YNQoL=s630-fcrop64=1,00000086ffffff78",
            "height":531,
            "width":940
         },
         "coverInfo":{
            "topImageOffset":0,
            "leftImageOffset":0
         }
      }
   }
}
```

# Simple apache-like password protection

Sometimes, I simply want to password protect an endpoint but not have to create any UI or such to getting it to work. Sometime like the Apache htpasswd type of auth. Here is how to do that in a node app.

Simple. Here is a package: [http-auth](https://github.com/http-auth/http-auth) you can link into your existing express app, passport, or other method to add Apache like htpasswd style login to your endpoint.
