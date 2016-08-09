---
name: Custom views
---

# Creating a custom view

You can create custom views that render in the interface builder

```
import UIKit

@IBDesignable
class UICustomButton: UIButton {

    @IBInspectable var normalTextColor: UIColor = UIColor.lightGrayColor() {
        didSet {
            setTitleColor(normalTextColor, forState: UIControlState.Normal)
        }
    }

    @IBInspectable var selectedBackgroundColor: UIColor = UIColor(red: 37.0/255.0, green: 147.0/255.0, blue: 1.0/255.0, alpha: 1.0) {
        didSet {
            updateColor()
        }
    }

    override var enabled: Bool {
        didSet {
            updateColor()
        }
    }

    override var selected: Bool {
        didSet {
            updateColor()
        }
    }

    override init(frame: CGRect) {
        super.init(frame: frame)

        customInit()
    }

    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)

        customInit()
    }

    /// Overriden method must call super.customInit().
    func customInit() {
        configureFont()
        configureColor()
    }

    func configureFont() {
    }

    func configureColor() {
    }

    func updateColor() {
        backgroundColor = enabled ? (selected ? selectedBackgroundColor : normalBackgroundColor) : disabledBackgroundColor
    }

}
```

*Possible issues you may run into:*

* Build warning saying code not key value compliant or that the file is missing.
Solution: Check the module for the view in the storyboard. Change the name of the module to the name of your project and try rebuilding again to see if error still appears. If you have many different build flavors like I usually do (debug, beta, release) you may need to change the module to another module name such as "Debug". Use the drop down arrow to select the module name.

![](/docs/images/ios_custom_view_module.png)

*Note: [I have heard](http://stackoverflow.com/a/33687844/1486374) that `dynamic` might be required in swift 2.0 for variables to work? Cannot confirm issues yet.*

The important parts to note are the `@IBDesignable` and `@IBInspectable` as well as the 2 constructors. `@IBDesignable` tells the storyboard to render the view and show the changes in the interface builder. If you do not include it, the class will still work at runtime but you will not be able to view it live in the interface builder.

`@IBInspectable` is cool because it will create fields in your interface builder that allows you to edit the variable value in the storyboard. This allows you to create custom views that devs can design their custom views in the storyboard dynamically.

*Note: If you want to share your custom view with others in a library, you must declare this class `public` and make every function and variable `public` as well.*

Notes:

* [Official docs](https://developer.apple.com/library/ios/recipes/xcode_help-IB_objects_media/Chapters/CreatingaLiveViewofaCustomObject.html)
* If you have a camera view or video player view, at runtime you want to show a video or camera. But in your interface builder you want to show a static image. [The official docs](https://developer.apple.com/library/ios/recipes/xcode_help-IB_objects_media/Chapters/CreatingaLiveViewofaCustomObject.html) at the bottom shares how to do this.
* [Library](https://github.com/andrew8712/DCKit) with a few custom views that work well with interface builder.

# Create UIView from XIB file

[Thanks to this gist](https://gist.github.com/bwhiteley/049e4bede49e71a6d2e2) for the inspiration for this.

Sometimes you want to create a custom view and it is easier to create via XIB then IB or code. Here is how to take a XIB and turn it into a UIView

* Create your XIB file in XCode. Use IB to create the XIB layout, add autolayout constraints.

* Create your code file that will link to this XIB:

```
import Foundation
import UIKit

class FooView: UIView {

    @IBOutlet weak var titleLabel: UILabel!
    @IBOutlet var contentView: UIView!

    override init(frame: CGRect) {
        super.init(frame: frame)

        self.commonInit()
    }

    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)

        self.commonInit()
    }

    private func commonInit() {
        NSBundle.mainBundle().loadNibNamed("NameOfXIBFileHere", owner: self, options: nil)

        guard let content = contentView else { return }

        content.frame = self.bounds
        content.autoresizingMask = [.FlexibleHeight, .FlexibleWidth]

        self.addSubview(content)
    }

    func setTitle(title: String) { <------ I created this used to change titleLabel's text.
        titleLabel.text = title
    }

}
```

In your XIB file, make the File Owner to this new class you just created, `FooView` (Don't set the root UIView, set the File Owner). Then, create an outlet from the XIB root UIView to `contentView`. Then create any other outlets (I created one here called `titleLabel`) and actions you want as a normal IB layout.

* Anywhere in code, you may now use this class:

```
let view: FooView = FooView()
// add view to subview, change constraints, whatever you want. It is just a UIView!
// view.setTitle() if you need to interact with the view.

// NOTE: make sure to set a frame for view. Or else, the view will be height and width of 0 and any click listeners you set will not be triggered. 
view.frame = CGRect(x: 0, y: 0, width: 150, height: 40)
```
