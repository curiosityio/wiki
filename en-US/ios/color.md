---
name: Color
---

# Create a gradient UIView going vertically

[Help from](http://stackoverflow.com/a/23074591/1486374)

```
let view = UIView(frame: CGRect(x: 0, y: 0, width: 320, height: 50))
let gradient = CAGradientLayer()

gradient.frame = view.bounds
gradient.colors = [UIColor.white.cgColor, UIColor.black.cgColor]

view.layer.insertSublayer(gradient, at: 0)
```

Of course, change `view` to whatever you need. You could use the background of a ViewController for example with `self.view` inside of a ViewController.

# Change direction of a gradient

[Help from](http://stackoverflow.com/a/20387923/1486374)

Use `startPoint` and `endPoint` to change direction of gradient layer.

The startPoint and endPoint properties of a CAGradientLayer are defined in the “unit coordinate system”. In the unit coordinate system:

(0,0) corresponds to the smallest coordinates of the layer's bounds rectangle, which on iOS is its upper-left corner unless the layer has been transformed;
(1,1) corresponds to the largest coordinates of the layer's bounds rectangle, which on iOS is its lower-right corner unless the layer has been transformed.
Thus arranging your gradient the way you want should be this simple:

```
let gradient = CAGradientLayer()

gradient.frame = view.bounds
gradient.colors = [UIColor.white.cgColor, UIColor.black.cgColor]

gradient.startPoint = CGPointZero;
gradient.endPoint = CGPointMake(1, 1);
```

# Create a radial gradient (gradient that starts in center and goes out towards edge of circle)

First, create this file:

```
import Foundation
import UIKit

// http://stackoverflow.com/a/31854064/1486374
// Allows you to create a radial gradient (circular gradient starting in center and going out).
// Used in UIViewExtensions to add a radial gradient background to a view.
class RadialGradientLayer: CALayer {

    var center:CGPoint = CGPoint(x: 50, y: 50)
    var radius:CGFloat = 20
    var colors:[CGColor] = [UIColor(red: 251/255, green: 237/255, blue: 33/255, alpha: 1.0).cgColor , UIColor(red: 251/255, green: 179/255, blue: 108/255, alpha: 1.0).cgColor]

    override init() {
        super.init()

        needsDisplayOnBoundsChange = true
    }

    init(center: CGPoint, radius: CGFloat, colors: [CGColor]) {
        self.center = center
        self.radius = radius
        self.colors = colors

        super.init()
    }

    required init(coder aDecoder: NSCoder) {
        super.init()
    }

    override func draw(in ctx: CGContext) {
        ctx.saveGState()

        let colorSpace = CGColorSpaceCreateDeviceRGB()
        let gradient = CGGradient(colorsSpace: colorSpace, colors: colors as CFArray, locations: [0.0,1.0])

        ctx.drawRadialGradient(gradient!, startCenter: center, startRadius: 0.0, endCenter: center, endRadius: radius, options: CGGradientDrawingOptions(rawValue: 0))
    }

}
```

Then, let's use it. I think it's best to put this into an extension:

```
import UIKit

extension UIView {

    func addCenteredRadialGradient(_ centerOfGradientColor: UIColor, outsideOfGradientColor: UIColor) {
        let centerPoint = CGPoint(x: 0.5 * self.frame.size.width, y: 0.5 * self.frame.size.width)
        let radius = min(self.frame.size.width, self.frame.size.height) * 0.5

        let layout = RadialGradientLayer(center: centerPoint, radius: radius, colors: [centerOfGradientColor.cgColor, outsideOfGradientColor.cgColor])
        layout.frame = self.bounds
        layout.frame.origin.y = self.bounds.height / 2 - radius
        layout.setNeedsDisplay()
        self.layer.insertSublayer(layout, at: 0)
        self.backgroundColor = outsideOfGradientColor
    }

}
```

Call the extension on a UIView:

```
self.view.addCenteredRadialGradient(UIColor.init(hexString: "#ffffff"), outsideOfGradientColor: UIColor.init(hexString: "#000000"))
```
