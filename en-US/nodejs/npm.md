---
name: npm
---

# Create CLI npm program

In your project's `package.json` file, there are a couple special pieces of meta data to add:

```
{
  "name": "bash-reminder",
  "version": "0.2.1",
  "description": "Display a message in terminal to yourself. Great for reminding yourself to do something.\"",
  "preferGlobal": true,
  "bin": {
    "bash-reminder": "bin/bash-reminder"
  }
}
```

The `bin` is pretty special in that it tells npm the name of the CLI executable we want to create so when the user installs your program globally, they will use `back-reminder` as the name of the executable.

Then, `bin/bash-reminder` is the path to the bash script to run. Here is an example of that:

```
#!/usr/bin/env node

var program = require('commander')
var chalk = require('chalk')

program
  .version(require('../package').version)
  .usage('reminder "Remind me to do something."')
  .command('reminder', 'text of the reminder you want to create')

program.on('--help', function () {
  console.log('Example: bash-reminder "Do this"')
})

function help() {
  program.parse(process.argv)
  if (program.args.length < 1) return program.help()
}
help()

var colors = [chalk.red, chalk.green, chalk.yellow, chalk.blue, chalk.magenta, chalk.cyan, chalk.white, chalk.gray]
var chalkColor = colors[Math.floor(Math.random() * colors.length)]

console.log(chalkColor(program.args[0]))
```

This is a simple program that uses the package [commander](https://github.com/tj/commander.js/) to create the CLI interface for you in a super handy way. The top code specifies your API to use your CLI program. Then, as long as the user runs your program correctly, then it will continue executing down the file.

# Publish package to npm registry.

This is pretty simple. As long as your package.json file is correct (I will assume it is. Especially if created via `npm init`) and the name of your package has not already been taken (check https://www.npmjs.com/package/<name-of-your-package> to see if it 404s) then you can package to the public super easy.

* Create an account on npmjs.com.

* `npm login` or `yarn login`

* `npm publish` or `yarn publish`

Done. 
