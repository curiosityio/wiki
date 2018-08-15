---
name: RxJava Testing
---

# Mocking schedulers 

When you have `Schedulers.io()` or `AndroidSchedulers.mainThread()` in your code for a unit test that you want to create, you need to mock the schedulers or else you will get an error when RxJava wants to actually run the code. 

* First, create an interface that you can inject into your object that you are trying to unit test. 

```
import io.reactivex.Scheduler

interface SchedulersProvider {
    fun io(): Scheduler
    fun mainThread(): Scheduler
}
```

* In your actual app code, anytime that you need to get a io or mainThread scheduler, you can inject this *real* object that will return the appropriate schedulers for your app to run: 

```
import io.reactivex.Scheduler
import io.reactivex.android.schedulers.AndroidSchedulers
import io.reactivex.schedulers.Schedulers

class AppSchedulersProvider: SchedulersProvider {

    override fun io(): Scheduler = Schedulers.io()

    override fun mainThread(): Scheduler = AndroidSchedulers.mainThread()

}
```

* Now, when you want to test, you can now mock the interface and return whatever you want for the `io()` and `mainThread()` functions. 

```
@RunWith(MockitoJUnitRunner::class)
class FooViewModelTest {

    @Mock private lateinit var schedulers: SchedulersProvider

    @Rule @JvmField val rule = InstantTaskExecutorRule()

    private lateinit var viewModel: FooViewModel

    @Before
    fun setUp() {
        `when`(schedulers.io()).thenReturn(Schedulers.trampoline())
        `when`(schedulers.mainThread()).thenReturn(Schedulers.trampoline())
        viewModel = FooViewModel(repository, schedulers)
    }

    @Test
    fun observeAnnouncements_deliversResult() {
        ...

        verify(schedulers).io()
        verify(schedulers).mainThread()
    }

}
```

The magic here is that we are returning `Schedulers.trampoline()` for the schedulers. This allows Rx to run appropriately and also allows us to check the mock to assert that the `io` or `mainThread` functions were called to get a scheduler. 