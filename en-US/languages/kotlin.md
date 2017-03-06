---
name: Kotlin
---

# Visitor pattern

* Create visitor enum

```
private enum class FavoriteSport {
    FOOTBALL {
        override fun <E> accept(visitor: Visitor<E>): E = visitor.visitFootball()
    },
    BASEBALL {
        override fun <E> accept(visitor: Visitor<E>): E = visitor.visitBaseball()
    };

    interface Visitor<out E> {
        fun visitFootball(): E
        fun visitBaseball(): E
    }

    abstract fun <E> accept(visitor: Visitor<E>): E
}
```

* Use in your code to return any value. Maybe a boolean or an object. Whatever:

```
val sportName = favoriteSport.accept(object : FavoriteSport.Visitor<String> {
    override fun visitFootball(): String {
        return "Football"
    }
    override fun visitBaseball(): String {
        return "Baseball"
    }
})
```

# Find differences between 2 lists.

To make this easier, I use Google's DiffUtils open source library. It allows you to simply give it 2 lists and it finds the insertions, deletions, and chanages between them.

* Install the library. Below is for Android gradle. You can find the jar, maven links, etc. [here](https://mvnrepository.com/artifact/com.googlecode.java-diff-utils/diffutils/1.3.0).

```
compile 'com.googlecode.java-diff-utils:diffutils:1.3.0'
```

* Find the differences between 2 lists.

```
val deltas = DiffUtils.diff(oldListOfData, newListOfData).deltas
deltas.forEach { delta ->
    if (delta.type == Delta.TYPE.INSERT) {
        val listPositionOfInsert = delta.revised.position
        val sizeOfInsert = delta.revised.size()
    } else if (delta.type == Delta.TYPE.DELETE) {
        val listPositionOfDelete = delta.original.position
        val sizeOfDelete = delta.original.size()
    } else { // delete.type is Delta.TYPE.CHANGE.
        val listPositionOfChange = delta.getRevised().getPosition()
        val sizeOfChange = delta.getRevised().size()
    }
}
```
