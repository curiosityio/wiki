---
name: Mailgun
---

# Verify Mailgun webhook came from Mailgun

```
var crypto = require('crypto') // built in node module

const mailgunApiKey: ?string = process.env.API_MAILGUN_API_KEY
if (!mailgunApiKey) { throw new Error('Must set API_MAILGUN_API_KEY environment variable.') }

var timestamp: string = req.body.timestamp
var token: string = req.body.token
var signature: string = req.body.signature

var computedHash: string = crypto.createHmac('sha256', mailgunApiKey)
        .update(timestamp.concat(token))
        .digest('hex')
if (computedHash != signature) { return res.status(406).send(new MailgunWebhookDontRetryError('Rejected webhook.')) } // send back 406 response.
```

* Concat the timestamp and token received in webhook.
* Hash ^^^ with SHA256 using your Mailgun API key as the secret.
* If the ^^^ hash and the signature given in the webhook are the same, it is valid! 
