---
name: Email
---

# Send email via mailgun 

* `npm install mailgun-js --save`

* Code sample below 

```
const API_KEY: ?string = process.env.API_MAILGUN_API_KEY
const DOMAIN: ?string = process.env.EMAIL_FROM_DOMAIN
const FROM_EMAIL_NAME: ?string = process.env.EMAIL_FROM_NAME
const FROM_EMAIL: ?string = process.env.EMAIL_FROM_EMAIL_ADDRESS

if (!API_KEY || !DOMAIN || !FROM_EMAIL_NAME || !FROM_EMAIL) { throw new Error(`Must set all of the email environmental variables in ${__dirname}/${__filename} before sending emails.`) }
const mailgun: Object = require('mailgun-js')({apiKey: API_KEY, domain: DOMAIN})

var data = {
    from: FROM_EMAIL_NAME + ' <' + FROM_EMAIL + '>',
    to: "to@example.com,
    subject: "subject here",
    html: "<h1>HTML string here</h1>"
}

return mailgun.messages().send(data, (error: Error, body: string): Promise<void> => {
    if (error) { return Promise.reject(error) }
    return Promise.resolve()
})
```

*Note: above, `html` parameter in data means that we have a html string that we want to send a html email via mailgun. We can replace that with `text` and pass in a plain text string to send as a plain text email instead of html email. Also, in this document is a handy way to generate HTML strings from HTML files and variables we can inject into these files.*

# Create dynamic HTML strings from HTML files 

* `npm install mustache --save`

* Code sample below 

```
import mustache from 'mustache'

const getHtmlString: (templateName: string, params: Object) => Promise<string> = (templateName: string, params: Object): Promise<string> => {
  return new Promise((resolve: Function, reject: Function) => {
    fs.readFile(__dirname + '/templates/' + templateName, "utf-8", (err: ?Error, html: string): void => {
      if (err) { return reject(err) }
      return resolve(mustache.render(html, params))
    })
  })
}
```

This is assumed that you have a directory, `templates` located in the same directory this code above is hosted and your `.html` files are located inside of it. 

* Now, where do you get HTML files from? I generate mine from [Mailchimp](https://mailchimp.com/). You can use their builder to create a template file and download it as raw HTML. This is way better then creating it yourself because email HTML is a mess. 

In the `.html` file you downloaded, go inside and create variables for yourself. Anywhere you want some text injected into the file dynamically, create variables using [mustachejs](https://github.com/janl/mustache.js) template strings like this: `{{variable_name_here}}`, `{{user_email_address}}`. If you need your variable to be escaped (such as a URL), add a `&` before your variable name like this: `{{&download_link}}`. 