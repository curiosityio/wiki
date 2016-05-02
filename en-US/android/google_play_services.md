---
name: Google Play Services
---

# Check installed on Android app.

* In your app's main activity:
```
private static final int GOOGLE_PLAY_SERVICES_INSTALL_CODE = 0;

@Override
protected void onResume() {
    super.onResume();

    if (checkPlayServicesInstalled()) {
        // do stuff here using google play services as you now know it is installed.
    }
}

protected boolean checkPlayServicesInstalled() {
    GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();

    int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
    if (resultCode != ConnectionResult.SUCCESS) {
        if (apiAvailability.isUserResolvableError(resultCode)) {
            apiAvailability.getErrorDialog(this, resultCode, GOOGLE_PLAY_SERVICES_INSTALL_CODE).show();
        } else {
            // this device is not supported so close the app.

            finish();
        }

        return false;
    }

    return true;
}
```
