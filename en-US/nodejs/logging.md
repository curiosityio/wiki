---
name: Logging
---

# Log messages to Slack channel.

* Create a private or public Slack channel where you would like notifications to show up.

* Add the "Incoming webhook" integration to this Slack channel you created.

* Install `winston` and `winston-slacker` npm modules to your node project.

```
$> npm install winston --save # or yarn add winston
$> npm install winston-slacker --save # or yarn add winston-slacker
```

* In your main script where your Node server starts, configure winston:

```
var winston = require('winston')
var winstonSlacker = require('winston-slacker')

if (process.env.NODE_ENV === "production") {
    if (process.env.SLACK_WEBHOOK_URL && process.env.SLACK_CHANNEL) {
        var winstonSlackerOptions = {
            webhook: process.env.SLACK_WEBHOOK_URL,
            channel: process.env.SLACK_CHANNEL,
            username: "Prod status bot"
        }
        winston.add(winstonSlacker, winstonSlackerOptions)
    }
}
```

You can change the `SLACK_WEBHOOK_URL` and `SLACK_CHANNEL` variables to another name if you choose. Those are the names of the environment variables you are setting to configure the Slack connection.

* Set the environment variables on your computer, Docker, Heroku, whatever.

```
export SLACK_CHANNEL=name-of-channel-you-created # name does *not* include any symbol. Just name of channel.
export SLACK_WEBHOOK_URL=https://hooks.slack.com/services/IWFWOE/WFJWEFIJEF/WFJEIFJEIFEIFEJFIE
```

* Done!

Whenever you want to log to Winston, you run: `winston.log('error', 'Stuff in here.')`
