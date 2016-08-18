---
name: Popovers
---

![](/docs/images/ios_popover.png)

# How to implement popover

* Create a storyboard for the popover, or create a new ViewController in an existing Storyboard. Long story short, create a ViewController in interface builder to create the layout for the popover.

* Create a ViewController file for this popover:

```
import UIKit

class FooPopoverViewController: BasePopoverViewController {

    override func getPreferredContentSize() -> CGSize {
        return CGSize(width: 200, height: 100)
    }

}

// Extends this:
import UIKit

class BasePopoverViewController: UIViewController {

    func setupPopover(delegate: UIPopoverPresentationControllerDelegate, arrowDirection: UIPopoverArrowDirection, sourceView: UIView?, sourceRect: CGRect) {
        modalPresentationStyle = .Popover
        preferredContentSize = getPreferredContentSize()

        if let popoverController = popoverPresentationController {
            popoverController.delegate = delegate
            popoverController.permittedArrowDirections = arrowDirection
            popoverController.sourceView = sourceView
            popoverController.sourceRect = sourceRect
        }
    }

    // override in child instances to specify size.
    func getPreferredContentSize() -> CGSize {
        return CGSize(width: 200, height: 200)
    }

    func dismiss(sender: AnyObject) {
        self.dismissViewControllerAnimated(true, completion: nil)
    }

}
```

In your ViewController in interface builder, set the ViewController to FooPopoverViewController.

In this FooPopoverViewController, you can add outlets, actions, etc. It is a ViewController. Treat it like one and do whatever you need to there.

* In the ViewController that you want to show the popover in, add code to show it:

```
import UIKit

class MainViewingViewController: UIViewController, UIPopoverPresentationControllerDelegate {

    var fooPopover: FooPopoverViewController?

    // when screen rotates, dismiss popover.
    override func viewWillTransitionToSize(size: CGSize, withTransitionCoordinator coordinator: UIViewControllerTransitionCoordinator) {
        fooPopover?.dismiss(self)

        super.viewWillTransitionToSize(size, withTransitionCoordinator: coordinator)
    }

    // anywhere now, call showPopover() to show it.
    func showPopover() {
        fooPopover = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("FooPopoverViewControllerId") as? FooPopoverViewController
        fooPopover?.setupPopover(self, arrowDirection: .Up, sourceView: self.viewToPutPopoverBelow, sourceRect: CGRect(x: self.viewToPutPopoverBelow!.bounds.midX, y: self.viewToPutPopoverBelow!.bounds.midY + 15, width: 0, height: 0))

        self.presentViewController(fooPopover!, animated: true, completion: nil)
    }    

    // this is needed as UIPopoverPresentationControllerDelegate to display the popover as a popover. If you don't have this, it will be shown full screen.
    func adaptivePresentationStyleForPresentationController(controller: UIPresentationController) -> UIModalPresentationStyle {
        return .None
    }
}
```
