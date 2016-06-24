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

# Access view controller inside of UIContainer

If you have a container, you can access the view controller inside of the container:

* In the embed segue setup connecting your container to view controller in storyboard, set the identifier to something (I usually set it to the name of the destination view controller name). We will be using this segue name below in the parent view controller code. Also, make sure that the embed segue destination view controller is set.

* Parent view controller code:

```
class CustomViewController: UIViewController {
    func myMethod() {}
}

class MainViewController: UIViewController {

    private var embeddedViewController: CustomViewController!

    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        if let vc = segue.destinationViewController as? CustomViewController
            where segue.identifier == "EmbedSegue" {

            self.embeddedViewController = vc
        }
    }

    override func viewDidAppear(animated: Bool) {
        self.embeddedViewController.myMethod()
    }
}
```

Notice the segue.identifier name set in the code above. This needs to be the name that you set in the previous step.

Thank you [reference doc](http://stackoverflow.com/a/29582305/1486374)

---

Above gives a great way for the parent to learn about the child. For code in the child to reach the parent, use `self.parentViewController` from child view controller.
