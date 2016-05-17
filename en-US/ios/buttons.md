---
name: Buttons
---

# Buttons with rounded corners and border

```
import UIKit

@IBDesignable
class UIRoundedButton: UIButton {

    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)

        setView()
    }

    override init(frame: CGRect) {
        super.init(frame: frame)

        setView()
    }

    func setView() {
        self.backgroundColor = UIColor.clearColor()
        self.layer.cornerRadius = 5
        self.layer.borderWidth = 1
        self.layer.borderColor = self.tintColor.CGColor
    }

}
```

In storyboard set your UIButton to UIRoundedButton. Your storyboard will show the rounded border on your view in the interface builder now because you set `@IBDesignable` and include the 2 constructors of the class (required or the storyboard will crash). 

# Globally set appearance of buttons

In app delegate `application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?)`:

```
UIButton.appearanceWhenContainedInInstancesOfClasses([UIViewController.self]).backgroundColor = UIColor.clearColor()
```

This will give all of your buttons in the app a clear background because all buttons appear in viewcontrollers.
