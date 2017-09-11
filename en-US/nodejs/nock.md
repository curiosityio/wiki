---
name: Nock
---

When testing APIs, you may want to mock real API interactions. This is where [nockjs](https://github.com/node-nock/nock) comes in.

# Mock an API call

```
var nock = require('nock');

describe('Stripe webhooks test.', function() {

    beforeEach(function() {
        nock.disableNetConnect();
        nock.enableNetConnect('127.0.0.1');
    });

    afterEach(function() {
        server.close();
    });
    it('should get Stripe event.', function testIt(done) {
        async.waterfall([
            function setupNock(callback) {
                var eventId = uid2(20);
                var stripeAccountId = uid2(20);

                // *NOTE:* The endpoint you send to nock is sensitive. If you send it /v1/foo/ but the endpoint is actually /v1/foo then it will not work. Make sure to get it exactly right. For Stripe, I have found the trailing `/` makes Nock not work with their endpoints.
                nock('https://api.stripe.com')
                .get('/v1/events/' + eventId, function(body) {
                    return true;
                })
                .reply(200, {
                    "created": 1326853478,
                    "livemode": false,
                    "id": eventId,
                    "type": eventType,
                    "user_id": stripeAccountId,
                    "object": "event",
                    "request": null,
                    "data": {
                        "object": null
                    }
                });

                callback(null, eventId);
            },
            function test(eventId, callback) {
                var body = {
                    "created": 1326853478,
                    "livemode": false,
                    "id": eventId,
                    "type": eventType,
                    "user_id": stripeAccountId,
                    "object": "event",
                    "request": null,
                    "data": {
                        "object": null
                    }
                };

                request(server)
                .post('/webhooks/stripe')
                .send(body)
                .end(function(err, res) {
                    res.status.should.be.equal(200);
                });
            }
        ]);
    });
});
```

Out of this long test, the part we really care about is:

```
nock('https://api.stripe.com') <---- base URL
.get('/v1/events/' + eventId, function(body) { <----- the request. GET, /v1/events/:eventid is the path, function(body) { return true; } is specifying the body. Here, we accept all bodies no matter what is sent in. We can specify a specific body if we want here.
    return true;
})
.reply(200, { <------ nock will return a 200 status along with this JSON body.
    "created": 1326853478,
    "livemode": false,
    "id": eventId,
    "type": eventType,
    "user_id": stripeAccountId,
    "object": "event",
    "request": null,
    "data": {
        "object": null
    }
});
```

Also, you may have noticed:

```
beforeEach(function() {
    nock.disableNetConnect(); <------ disable all real internet connection in tests.
    nock.enableNetConnect('127.0.0.1'); <------ enable specific internet connections. Here, allow localhost to allow calling API locally on our machine.
});
```

# Debugging

(from [official doc](https://github.com/node-nock/nock#recording))
Guessing what the HTTP calls are is a mess, specially if you are introducing nock on your already-coded tests. For these cases where you want to mock an existing live system you can record and playback the HTTP calls like this:

```
nock.recorder.rec();
// Some HTTP calls happen and the nock code necessary to mock
// those calls will be outputted to console
```

Recording relies on intercepting real requests and answers and then persisting them for later use.

**ATTENTION!: when recording is enabled, nock does no validation. Therefore, you want to use the rec() function above, view the console output, write your nock statements, then delete the rec() function call.**

---

Another huge debugging tip is to enable logging of Nock to the console:

```
DEBUG=nock.* npm run test
```
