---
name: Navigation
---

# UINavigationBar and UINavigationController

# Embed a ViewController in a UINavigationController

Here is one way to do it with code:

* In your AppDelegate:

```
let viewControllerToEmbed: FooViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("ViewControllerIdHere") as! FooViewController

// do stuff to viewControllerToEmbed such as set delegate, etc.

let navigationController = UINavigationController()
navigationController.viewControllers = [viewControllerToEmbed]

self.window?.rootViewController = navigationController
self.window?.makeKeyAndVisible()
```

---

Or, you may use Storyboard. Create a ViewController in Interface Builder. Select the ViewController > Editor > Embed In > Navigation Controller.

# Style UINavigationBar

## Remove hairline bottom border UINavigationBar

Well, this doesn't "remove" it, but sets the color of it. Set the color of it to the same color of the UINavigationBar's bar tint color to make it seem invisible. *Note: If your UINavigationBar's translucent property is set to true, the color will seem a little off. The bottom hairline is not translucent but the bar is therefore, the colors will be off.*

```
import UIKit

extension UINavigationBar {

    func setBottomHairlineColor(color: UIColor) {
        let navBorder: UIView = UIView(frame: CGRect(x: 0, y: frame.size.height, width: frame.size.width, height: 1))
        navBorder.backgroundColor = color
        navBorder.opaque = true
        addSubview(navBorder)
    }

}
```

# Set colors

```
navigationBar.barTintColor = UIColor.greenColor()  <------ background color of bar.
navigationController.navigationBar.tintColor = UIColor.whiteColor()  <------ color of bar button items.
navigationController.navigationBar.translucent = false  <---- If this is true, the barTintColor will seem off of what you set it as.

// Below, how to set color of title text.
let titleDict: NSDictionary = [NSForegroundColorAttributeName: UIColor.whiteColor()]
navigationController.navigationBar.titleTextAttributes = titleDict as? [String : AnyObject]
```
