---
name: UIViewController
---

# Launch a view controller from another view controller

Say for instance the user logs out of your app and you want to present to them the login view controller to login again.

We take a storyboard by it's filename, and initialize a view controller by it's storyboard ID inside of the storyboard.

* Open your storyboard, open your ViewController you want to present (in this example FooViewController). Where you set the view controller's class name, you will see a property you can set called "Storyboard ID":

![](/docs/images/set_storyboard_id_view_controller.png)

Set the storyboard ID here that we will refer to below:

* In your ViewController code that you want to present the view controller:

```
let fooViewController: FooViewController = UIStoryboard(name: "NameOfStoryboardFileHere", bundle: nil).instantiateViewControllerWithIdentifier("StoryboardIDOfViewControllerHere") as! FooViewController

fooViewController.delegate = self
fooViewController.setFooData(data)

presentViewController(fooViewController, animated: true, completion: nil)
```

If you would like to dismiss the current view controller and then launch the new one, do the following (this is good because if you were to launch the new view controller and then rotate the screen, the old ViewController is still the root and therefore, will be shown to user unless you dismiss it):

```
dismissViewControllerAnimated(false) {
    self.presentViewController(fooViewController, animated: true, completion: nil)
}
```

(`NameOfStoryboardFileHere` would be `Foo` for example if file was named `Foo.storyboard`)

Call presentViewController is where the current view controller presents the new view controller and goes away.

## Launch view controller from another view controller and present it with a navigation bar.

If you want to display a new view controller with it's very own navigation bar which is nice to add "Done" buttons to make the new view controller overlay the existing view controller and then dismiss easily (below is an example):

![](/docs/images/presenet_view_controller_nav_bar.png)

Do the same as above with one little addition:

```
let fooViewController: FooViewController = UIStoryboard(name: "NameOfStoryboardHere", bundle: nil).instantiateViewControllerWithIdentifier("StoryboardIDOfViewControllerHere") as! FooViewController

let navController = UINavigationController.init(rootViewController: fooViewController) // <-- create nav controller from fooViewController

fooViewController.delegate = self
fooViewController.setFooData(data)

presentViewController(navController, animated: true, completion: nil) // <--- present navController instead of fooViewController
```

In your storyboard

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
