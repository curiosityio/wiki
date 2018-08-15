---
name: Espresso 
---

# Resources 

* [Espresso cheat sheet](https://google.github.io/android-testing-support-library/docs/espresso/cheatsheet/index.html)
* [Android testing samples GitHub](https://github.com/googlesamples/android-testing)
* [Android testing support library](https://google.github.io/android-testing-support-library/docs/espresso/index.html)

# Custom ViewAction 

Let's say that you have a `BottomNavigationView` in your View that you want to test with Espresso. You want to test that if you press on a tab in the `BottomNavigationView` then a different `Fragment` View is shown. 

To do that, you would create a custom Espresso `ViewAction`. Espresso has many built-in `ViewAction`s but not enough to cover everything. 

```kotlin
fun selectMenuItemAtId(menuItemId: Int): ViewAction {
    return object : ViewAction {
        override fun getDescription() = "with menu item with id $menuItemId"

        override fun getConstraints() = allOf(isDisplayed(), isAssignableFrom(BottomNavigationView::class.java))

        override fun perform(uiController: UiController, view: View) {
            val bottomNavigationView = view as BottomNavigationView
            val menu = bottomNavigationView.menu
            val menuItem: MenuItem = menu.findItem(menuItemId) ?: throw RuntimeException("No menu item with id: $menuItemId")
            bottomNavigationView.selectedItemId = menuItem.itemId
        }
    }
}
```

Then to use: 

```kotlin 
onView(withId(R.id.fragment_main_bottom_nav_view)).perform(selectMenuItemAtId(R.id.profile))
```

