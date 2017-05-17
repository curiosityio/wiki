---
name: Status bar
---

# Set status bar color

I find it easiest to create an extension for `UIViewController`:

```
import UIKit

extension UIViewController {

    func setStatusBarColor(_ color: UIColor) {
        let statusBarBackground = UIView(frame: UIApplication.shared.statusBarFrame)
        statusBarBackground.backgroundColor = color
        self.view.addSubview(statusBarBackground)
    }

}
```

# Change text color to light/dark

In your `UIViewController`, override this function to provide a black text color (`.default`) or a white text color (`.lightContent`). Keep in mind that if you want the black text color, don't do anything. `.default` is the default behavior. 

```
override var preferredStatusBarStyle: UIStatusBarStyle {
    return .lightContent
}
```
