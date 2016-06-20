---
name: UIViewController
---

# Launch a view controller from another view controller

Say for instance the user logs out of your app and you want to present to them the login view controller to login again.

We take a storyboard by it's filename, and initialize a view controller by it's storyboard ID inside of the storyboard.

```
let fooStoryboard = UIStoryboard(name: "NameOfStoryboardHere", bundle: nil).instantiateViewControllerWithIdentifier("StoryboardIDOfViewControllerHere")
presentViewController(fooStoryboard, animated: true, completion: nil)
```

Call presentViewController is where the current view controller presents the new view controller and goes away. 
