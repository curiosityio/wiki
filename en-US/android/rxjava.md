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

* `.observeOn()` is specifying what thread the `.subscribe()` (and any future calls such as `.map()`, `.flatMap()`, etc.) will be called on.
* You can specify a `.subscribeOn()` if you want which specifies what thread the Data Manager's `getAllFooData()` will be called on. By default, it is whatever thread you are calling it from. In this case, it will be the UI thread as we are calling the above code from a Activity/Fragment.
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
