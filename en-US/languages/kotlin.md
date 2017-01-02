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
