---
name: Room 
---

# Date type in a Entity 

Room does not understand the `Date` type. You need to create a custom TypeConverter for Room that will convert the `Date` type into something that SQLite can store. 

* First, create the TypeConverter. 

```
import androidx.room.TypeConverter
import java.util.*

class DateTypeConverter {

    @TypeConverter
    fun fromTimestamp(value: Long?): Date? {
        return if (value == null) null else Date(value)
    }

    @TypeConverter
    fun dateToTimestamp(date: Date?): Long? {
        return date?.time
    }

}
```

* Second, tell Room about all of your TypeConverters. 

```
@Database(entities = [FooModel::class], version = 1, exportSchema = true)
@TypeConverters(DateTypeConverter::class)
abstract class Database: RoomDatabase() {
    abstract fun fooDao(): FooDao
}
```

* Now you can use a `Date` type in your Entity. 

```
@Entity(tableName = "foo")
class FooModel(...
               var created_at: Date = Date())
```