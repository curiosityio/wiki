---
name: Room testing 
--- 

# Testing migrations 

There is an [official doc](https://developer.android.com/training/data-storage/room/migrating-db-versions#test) on how to do this. 

# Perform integration tests on a Room database. 

To perform integration tests (tests that run on an actual Android device and performs actions on a real database) we will be using an in-memory database that is newly constructed and destroyed on every test. 

* In your `Database` that you have setup for your app to use Room, add a static function in there to construct a *test* database. 

```
@Database(entities = [FooModel::class], version = 1, exportSchema = true)
abstract class YourAppDatabase: RoomDatabase() {
    abstract fun fooDao(): FooDao

    companion object {
        fun testCreate(context: Context): Database {
            return Room.inMemoryDatabaseBuilder(context.applicationContext, YourAppDatabase::class.java).build()
        }
    }
}
```

* In your Android test class, you can now setup and use the database. 

```
@RunWith(AndroidJUnit4::class)
class RepositoryTest {

    @Mock private lateinit var service: AppApi

    private lateinit var db: YourAppDatabase

    private lateinit var repository: Repository

    @Before
    fun setup() {
        MockitoAnnotations.initMocks(this)

        db = YourAppDatabase.testCreate(InstrumentationRegistry.getTargetContext())
        repository = Repository(service, db)
    }

    @After
    fun tearDown() {
        db.close()
    }

    @Test
    fun doStuffWithDatabase() {
        // Now, you can do whatever you want with the database. 
        // I like to interact with the DAOs to perform actions for me such as inserting dummy data to setup a test. 
        // db.fooDao().insertDummyData(listOf(FooModel(), FooModel()))
    }    

}

```