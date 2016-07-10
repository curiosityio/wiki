---
name: UITableView
---

# Show/hide divider in tableview

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    let numberRows: Int = dataArray != nil ? dataArray!.count : 0

    if numberRows == 0 {
        tableView.separatorStyle = .None
    } else {
        tableView.separatorStyle = .SingleLine
    }

    return numberRows
}
```

# Dynamic height view cells

[Original doc for help with this](http://stackoverflow.com/a/18746930/1486374)

There are 2 steps to make your tableview cells be dynamic height.

* Setup auto layout constraints in storyboard. In your UITableViewCell subclass, add constraints so that the subviews of the cell have their edges pinned to the edges of the cell's contentView (most importantly to the top AND bottom edges). NOTE: don't pin subviews to the cell itself, only to the cell's contentView!

TL;DR add constraints so that the cell's contentView has it's inner views pinned to the top bottom. Also make sure at least 1 view does not have a view height constraint set so it is able to grow in height.

![](/docs/images/tableviewcell_dynamic_height_autolayout.png)

* In your code, add the following (works on iOS 8 and above. Check out [original post](http://stackoverflow.com/a/18746930/1486374) to learn about iOS 7):

```
self.tableView.rowHeight = UITableViewAutomaticDimension;
self.tableView.estimatedRowHeight = 44.0; // <---- 44.0 doesn't really matter. 44.0 has always worked for me, 1.0 does not work.
```

Or make a custom view to do it for you:

```
import UIKit

@IBDesignable
class DynamicCellHeightTableView: UITableView {

    override func drawRect(rect: CGRect) {
        rowHeight = UITableViewAutomaticDimension
        estimatedRowHeight = 44.0
    }

}
```

And use DynamicCellHeightTableView in storyboard as custom view for tableview.

# Pull to refresh

I created my own custom UITableView to contain all of this but you don't need to.

```
import UIKit

protocol PullToRefreshTableViewDelegate {
    func pullToRefreshTriggeredTableView()
}

class PullToRefreshTableView: UITableView {

    var pullRefreshDelegate: PullToRefreshTableViewDelegate?
    let refreshControl: UIRefreshControl = UIRefreshControl()

    override func drawRect(rect: CGRect) {
        super.drawRect(rect)

        refreshControl.attributedTitle = NSAttributedString(string: "Pull to refresh message here")
        refreshControl.addTarget(self, action: #selector(PullToRefreshTableView.refresh(_:)), forControlEvents: UIControlEvents.ValueChanged)

        addSubview(refreshControl)
    }

    func refresh(sender: AnyObject) {
        pullRefreshDelegate?.pullToRefreshTriggeredTableView()
    }

    func dismissPullToRefresh() {
        refreshControl.endRefreshing()
    }

}
```

# Set background color tableview

```
self.tableView.backgroundColor = yourColor
self.tableView.backgroundView.backgroundColor = yourColor
```
