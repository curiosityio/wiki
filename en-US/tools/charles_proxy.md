---
name: Charles Proxy
---

Get up and running on machine:

* [Download/install](https://www.charlesproxy.com/download/latest-release/)
* Go to Help > SSL Proxying > Install SSL root cert. This will ask you for your password to install it to your Mac keychain. Now, inside of your keychain, right click on the Charles proxy cert installed to your 'system' in the keychain and select 'Get info'. Drop down the trust carrot, where it says "When using this certificate" select "always trust" to trust the cert for SSL proxying.
* Tools > proxy settings. Make sure port is 8888. Go to Mac OSX tab and check enable osx proxying.

Now when you use your browser, it will proxy it through Charles.

# Proxy Android phone traffic

* On phone, go to settings > wifi > select your wifi you're connected to and select "modify network".
* Scroll down until you see Proxy settings. Select this and select "manual". Enter in your Mac's IP address (find in Charles via Help > Local IP address) and for port enter 8888 like we set in the settings when installing Charles. If you have Charles open on your computer, you should get a prompt asking if you want to trust a connection into Charles. Accept it.
* On your Mac, in Charles: Help > SSL Proxying > Install SSL root cert on mobile device. It will tell you to open a URL on your browser on your phone.

Now go to your phone's browser, try to go somewhere and you should see it on Charles.

# Filter what shows up on Charles recording panel

The left panel of proxied websites can get a little crazy on Charles. If you only want to show twitter.com connections for example:

* In Charle: Tools > Recording settings. Include tab. Add button. Then in host, enter *.twitter.com*

Now you will only see connections from Twitter.com in the recording list. 
