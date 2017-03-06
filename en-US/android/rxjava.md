---
name: RxJava
---

# Install

In your app/build.gradle file:

```
compile 'io.reactivex:rxandroid:1.2.1'
compile 'io.reactivex:rxjava:1.1.6'
compile 'com.trello:rxlifecycle:0.8.0'
```

# Create and observable

In your Activity/Fragment:

```
final Action1<Throwable> errorCallback = new Action1<Throwable>() {
    @Override
    public void call(Throwable throwable) {
        throwable.printStackTrace();
        DialogUtil.getSimpleOkDialog(getActivity(), getString(R.string.error), throwable.getMessage()).show();
    }
};

mDataManager.getAllFooData()
        .compose(FooFragment.this.<RealmResults<FooModel>>bindUntilEvent(FragmentEvent.PAUSE))
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Action1<RealmResults<FooModel>>() {
            @Override
            public void call(RealmResults<FooModel> fooModels) {
                populateFooRecyclerViewList(fooModels);
            }
        }, errorCallback);
```

Some notes here.

* `.observeOn()` modifies the chain below it to run on a specified Scheduler (thread). You can have many `.observeOn()` calls to continuously change the code below it to run on the specified thread.
* You can specify a `.subscribeOn()` if you want which specifies what thread the Observeable *starts* on.
* `.compose()` is built in for RxJava if you want to be able to add repeating functionality instead of having to repeat yourself over and over. Here, we are actually using RxLifecycle library's method to bind our observer to our fragment's lifecycle. This allows our observer to automatically be stopped when fragment's onPause is called! If we do not do this, our observer will continue on forever and ever. That's not good.

Then, the Data Manager's `getAllFooData()` function where it returns an Observable:

```
fun getAllFooData(): Observable<RealmResults<FooModel>?> {
    return Observable.create(
            object : Observable.OnSubscribe<RealmResults<FooModel>?> {
                override fun call(subscriber: Subscriber<in RealmResults<FooModel>?>?) {
                    subscriber?.onNext(fooDao.getAllFooDataUI()) // onNext() means there is new data! This will call the subscriber's methods. In this example, .subscribe() but we can use others.

                    fooApiManager.getFoo(object: BaseApiManager.ApiResponseCallback<Void> {
                        override fun success(data: Void?) {
                            subscriber?.onNext(draftsDao.getAllFooDataUI())
                            subscriber?.onCompleted() // tell subscriber to stop observing. We are done getting data.
                        }

                        override fun error(message: String?) {
                            subscriber?.onError(RuntimeException(message)) // tell subscriber there was an error. Will call the error callback.
                        }
                    })
                }
            })
}
```

# Global error listener

In Application file:

```
@Override
public void onCreate() {
    super.onCreate();

    RxJavaHooks.setOnError(new Action1<Throwable>() {
        @Override public void call(Throwable throwable) {
            if (!(throwable instanceof NoInternetConnectionException) && !(throwable instanceof ErrorFromApiException)) {
                LogUtil.error(throwable);
            }
        }
    });
    ...
}
```

# map()

`.map()` transforms items emitted by an Observable by applying a function to each item. You provide `.map()` a function, each item emitted from an Observable runs through that function.

![](/docs/images/rxjava_map.png)

```
----1---2---3---4---- Items emitted by source Observable
.map<Int>({ number -> number + 1})
----2---3---4---5---- Result emitted from .map()
```

The difference between `map()` and `flatMap()` is that `flatMap()` merges everything together into 1 Observable where `map()` emits X number of Observables where X is the number of items emitted by the source Observable.  

# flatMap()

`.flatMap()` transforms items emitted by an Observable by applying a function to each item *and then* merging them all into 1 final Observable. You provide `.flatMap()` a function, each item emitted from an Observable runs through that function.

![](/docs/images/rxjava_flatmap.png)

```
----1---2---3---4---- Items emitted by source Observable
.map<Int>({ number -> number + 1})
---2
---3 <-- these all get created, but not emitted yet. They get merged together below.
---4
---5
----2---3---4---5 Result emitted from .flatMap()
```

The difference between `map()` and `flatMap()` is that `flatMap()` merges everything together into 1 Observable where `map()` emits X number of Observables where X is the number of items emitted by the source Observable.  

# repeatWhen()

You want to repeat an Observable, huh?

```
Observable.create<FooData> { subscriber ->
    subscriber.onNext(fooData)
    subscriber.onCompleted() // <---- MUST call onCompleted. repeatWhen only runs when onCompleted is called.
}.repeatWhen { it.delay(1, TimeUnit.SECONDS) }.runOnBackgroundReportBackUIThread()
        .compose(bindUntilEvent<FooData>(FragmentEvent.STOP)).subscribe { status ->
            // this code gets run every second.
}
```

# Scenarios

* Have an Observable. Want to run another Observable after it, but use result of first one.

*Solution: Use  `.map()` or `.flatMap()` on the Observable.*
