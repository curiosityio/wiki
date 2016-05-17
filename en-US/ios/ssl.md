---
name: SSL
---

Apple blocks communication (iOS 9 and above) to any hostnames that are insecure via http://. You will get an error:

```
Transport security has blocked a cleartext HTTP (http://) resource load since it is insecure. Temporary exceptions can be configured via your app's Info.plist file.
```

In your app, you can allow insecure hostnames. Open your Info.plist file as source code (right click on Info.plist > open as > source code).

Paste in this code:

```
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSExceptionDomains</key>
  <dict>
    <key>yourserver.com</key>
    <dict>
      <!--Include to allow subdomains-->
      <key>NSIncludesSubdomains</key>
      <true/>
      <!--Include to allow HTTP requests-->
      <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
      <true/>
      <!--Include to specify minimum TLS version-->
      <key>NSTemporaryExceptionMinimumTLSVersion</key>
      <string>TLSv1.1</string>
    </dict>
  </dict>
</dict>
```

You can change `yourserver.com` to your insecure endpoint (it allows subdomains as well: foo.bar.com).

If you want the lazy method and allow all endpoints:

```
<key>NSAppTransportSecurity</key>
<dict>
  <!--Include to allow all connections (DANGER)-->
  <key>NSAllowsArbitraryLoads</key>
      <true/>
</dict>
```

I like to then open the Info.plist normally after I add this code in to make sure I didn't paste it incorrectly. (right click Info.plist > open as > Property List).
