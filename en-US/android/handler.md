---
name: Handlers
---

# Run code infinitely every interval amount of time

I would rather do this in RxJava, but here it is in Handler code anyway.

```
private static final int DO_SOMETHING_INTERVAL = 300000;
private Handler mDoSomethingHandler;
private Runnable mDoSomethingRunnable;
...

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ...
        mDoSomethingHandler = new Handler();
        ...
    }

    // don't have to do it in onresume, do it wherever you want to start running the code.
    @Override
    public void onResume() {
        super.onResume();

        mDoSomethingRunnable = new Runnable() {
            @Override
            public void run() {
                // do whatever code you need to run infinitely every amount of time interval here.

                mDoSomethingHandler.postDelayed(this, DO_SOMETHING_INTERVAL);
            }
        };
        mDoSomethingHandler.post(mDoSomethingRunnable); // post will run code right away. could postDelayed() to delay start.
    }

    // dont have to do in onstop(). do wherever you need to stop running the code.
    @Override
    public void onStop() {
        super.onStop();

        ...
        mDoSomethingHandler.removeCallbacks(mDoSomethingRunnable);
        ...
    }
```
