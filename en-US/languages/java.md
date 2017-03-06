---
name: Java
---

# Visitor pattern

* Create visitor enum

```
public enum FavoriteSport {

    FOOTBALL {
        @Override
        public <E> E accept(Visitor<E> visitor) {
            return visitor.visitFootball();
        }
    },
    BASEBALL {
        @Override
        public <E> E accept(Visitor<E> visitor) {
            return visitor.visitBaseball();
        }
    };

    public abstract <E> E accept(Visitor<E> visitor);

    public interface Visitor <E> {
        E visitFootball();
        E visitBaseball();
    }

}
```

* Use in your code to return any value. Maybe a boolean or an object. Whatever:

```
if (mFavoriteSport.accept(new FavoriteSport.Visitor<Boolean>() {
    @Override
    public Boolean visitFootball() {
        return false;
    }

    @Override
    public Boolean visitBaseball() {
        return true;
    }

})) {
    // whatever mFavoriteSport represents, it returns if its a favorite sport of yours.
}
```

# Find differences between 2 lists.

I wrote this up in the Kotlin doc. Go view it there. 
