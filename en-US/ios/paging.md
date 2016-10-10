---
name: Paging
---

# Implement UIPageViewController

[This blog post](https://spin.atomicobject.com/2015/12/23/swift-uipageviewcontroller-tutorial/) provides these images. Very good help, thank you!

![](/docs/images/uipageviewcontroller.gif)

* In Interface Builder, add a new page view controller element onto the canvas.

![](/docs/images/pageviewcontroller_canvas.png)

* Create a `UIPageViewController` subclass file and assign it as the view controller for the UIPageViewController interface builder element created above.

* Create 1+ view controllers in Interface Builder which will be the individual view controller(s) to page through.

![](/docs/images/page_controller_view_controllers.png)

* For each view controller you create in Interface builder, set Storybord IDs for them to inflate later. Create UIViewController subclasses and treat the view controllers are regular view controllers. They are view controllers, just displayed in a paging navigation view.

* Setup the page view controller

```
import UIKit

class FooPageViewController: UIPageViewController, UIPageViewControllerDataSource {    

    private var data: [FooModel] = []

    override func viewDidLoad() {
        super.viewDidLoad()

        dataSource = self

        // Setting loading view controller here while API call is performed. If don't set a view controller here, a black/transparent view controller is shown and it looks bad.
        self.setViewControllers([getLoadingViewController()], direction: UIPageViewControllerNavigationDirection.Forward, animated: true, completion: nil)
    }

    func getLoadingViewController() -> UIViewController {
        return UIStoryboard(name: "FooStoryboard", bundle: nil).instantiateViewControllerWithIdentifier("LoadingViewControllerId")
    }

    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)

        callApiGetData()
    }

    func getPageViewController(data: FooModel) -> BarViewController {
        let viewController: BarViewController = UIStoryboard(name: "BarStoryboard", bundle: nil).instantiateViewControllerWithIdentifier("BarViewControllerId") as! BarViewController

        viewController.barData = data

        return viewController
    }

    func callApiGetData() {
        // call API here to get data.
        // When API call done, set the view controllers again:
            self.data = dataFromApi

            self.setViewControllers([self.getPageViewController(self.data[0])], direction: UIPageViewControllerNavigationDirection.Forward, animated: false, completion: nil)
        }
    }

    // Called to get view controller if user swipes back to previous view controller. Return nil if no view controller available and user wont be able to swipe back.
    func pageViewController(pageViewController: UIPageViewController, viewControllerBeforeViewController viewController: UIViewController) -> UIViewController? {
        let currentBarDataShownToUser = (viewController as! BarViewController).barData

        guard let currentBarDataIndex = self.data.indexOf(currentBarDataShownToUser) else {
            return nil
        }

        let previousIndex = currentBarDataIndex - 1

        guard previousIndex >= 0 else {
            return nil
        }

        guard self.data.count > previousIndex else {
            return nil
        }

        return getPageViewController(self.data[previousIndex])
    }

    // Called to get view controller if user swipes forward to next view controller. Return nil if no view controller available and user wont be able to swipe forward.
    func pageViewController(pageViewController: UIPageViewController, viewControllerAfterViewController viewController: UIViewController) -> UIViewController? {
        let currentBarDataShownToUser = (viewController as! BarViewController).barData

        guard let currentBarDataIndex = self.data.indexOf(currentBarDataShownToUser) else {
            return nil
        }

        let nextIndex = currentBarDataIndex + 1

        guard nextIndex <= self.data.count else {
            return nil
        }

        guard self.data.count > nextIndex else {
            return nil
        }

        return getPageViewController(self.data[nextIndex])
    }

}
```

This is all customizable. Don't return nil for `viewControllerAfterViewController` function and it will create an infinite scroll. Always return nil for `viewControllerBeforeViewController` function to always go forward and never back.

In Interface Builder, you can change the swiping style for paging view controller:

![](/docs/images/swipe_style_pageviewcontroller.png)

# Detect what process step currently shown to user

In your UIPageViewController, implement UIPageViewControllerDelegate and then add this function.

```
func pageViewController(pageViewController: UIPageViewController, didFinishAnimating finished: Bool, previousViewControllers: [UIViewController], transitionCompleted completed: Bool){
    let currentlyShownViewController = pageViewController.viewControllers![0] as! ViewController
    let index = currentlyShownViewController.pageIndex
}
```

Done. *Note: this function is only called when the user swipes for pages 2+. The very first screen shown to user, this is not called. Therefore, when you call `self.setViewControllers()`, keep a reference to that view controller you set there as the first item. Then, future view controllers are obtained via this funciton above.*

Then one extra step I like to add is:

```
var currentlyShownViewController: UIViewController! {
    didSet {
        setNavigationBarTitle(currentlyShownViewController?.getTitleText() ?? "")
    }
}
```

To perform some action when you set the currentlyShownViewController.
