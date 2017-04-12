---
name: RxSwift
---

# Create a Single or Completable

You can use this handy way of using an extension:

```
extension PrimitiveSequence {

    public func subscribe(_ onNext: @escaping (ElementType) -> (), onError: @escaping (Error) -> ()) -> Disposable {
        var stopped = false
        return self.primitiveSequence.asObservable().subscribe { event in
            if stopped { return }
            stopped = true

            switch event {
            case .next(let element):
                onNext(element)
            case .error(let error):
                onError(error)
            case .completed:
                fatalError("Hit completed. This should never run.")
            }
        }
    }

}
```

Then, use the extension:

```
let disposeBag = DisposeBag()
Single<Bool>.create { observer in
        observer(SingleEvent.success(true))
        // or, do observer(SingleEvent.error(YourErrorHere)) to report an error.
        return Disposables.create()
    }
    .observeOn(MainScheduler.instance)
    .subscribe({ (boolValue) in
        // do something with boolValue
    }, onError: { (error) in
        // do something with error
    })
    .disposed(by: disposeBag)
```

Or do the vanilla RxSwift way:

```
let disposeBag = DisposeBag()
Single<Bool>.create { observer in
        observer(SingleEvent.success(true))
        // or, do observer(SingleEvent.error(YourErrorHere)) to report an error.
        return Disposables.create()
    }
    .observeOn(MainScheduler.instance)
    .subscribe { (event) in
        switch event {
        case .success(let boolValue):
            // do something with boolValue
            break
        case .error(let error):
            // do something with error 
            break
        }
    }
    .disposed(by: disposeBag)
```
