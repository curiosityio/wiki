---
name: Debugging
---

# Catch 'last resort' exceptions before your application crashes.

When I catch an error in my application in Express, I log it via the npm module, `winston` (which sends the error to a file and to Slack). I print the stacktrace and message of the error for myself to reference later:

```
app.use(function errorHandling(err, req, res, next) {
    winston.log('error', 'SYSTEM ERROR. Message: ' + err.message + ' stack: ' + err.stack)

    if (res.headersSent) {
        return next(err);
    }

    var message = (process.env.NODE_ENV === "development") ? err : 'System error. Please try again.';
    res.status(500).send(new SystemError(message));
});
```

But, sometimes my application crashes when I do not catch the exception. In this case, the errors simply print to stderr. I want to be able to log those as well. Nodejs provides an event listener for catching these exceptions.

```
process.on('uncaughtException', (err) => {
    winston.log('error', 'SYSTEM ERROR (caught in uncaughtException handler). Message: ' + err.message + ' stack: ' + err.stack)
})
```

[Docs on the event](https://nodejs.org/api/process.html#process_event_uncaughtexception)
