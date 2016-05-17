---
name: Segmented control
---

# Install segmented control to viewcontroller

* Add a segmented control to a viewcontroller's storyboard view.

* Create an action in the viewcontroller from the storyboard.

* Switch statement stating what position the segmented control is on.

```
private let inboxSegmentedControlIndex: Int = 0
private let archiveSegmentedControlIndex: Int = 1

@IBAction func emailsListSegmentedControlSwitched(sender: UISegmentedControl) {
    switch applicationsSegmentedControl.selectedSegmentIndex {
    case inboxSegmentedControlIndex:
        // TODO show inbox
        break
    case archiveSegmentedControlIndex:
        // TODO show archive
        break
    default:
        break
    }
}
```
