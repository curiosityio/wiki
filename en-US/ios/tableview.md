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

*Note:* When using this dynamic cell height, if there is a case where you have your cell content that would be *less than* 44px heigh, when you select a tableview cell you will notice a glitch as the tableview will refresh and shrink the cells.

In order to fix this, in autolayout, make sure that your dynamic view view's heights are set to >= height.

For example, here is a tableview with 1 cell and 1 dynamic height label. It is dynamic height, because I don't specify a maximum height. In fact I don't specify a height at all (part of the problem I am talking about above).

![](/docs/images/dyanmic_height_cell_no_height.png)

The label might make the cell actually go less then 44px heigh making the tableview cells blink in the UI. Add a >= height to the label so now the tableview cell will always be >= 44.

![](/docs/images/dyanmic_height_cell_height.png)

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

# Populate TableView from ViewController

* Needed code in your ViewController:

```
class FooViewController: BaseUIViewController, UITableViewDelegate, UITableViewDataSource { <--- include UITableViewDelegate and UITableViewDataSource

    @IBOutlet weak var fooTableView: UITableView!

    private var fooData: [FooModel] = []

    override func viewDidLoad() {
        super.viewDidLoad()

        fooTableView.delegate = self    <--- tell your tableview that your view controller is going to be providing the data and info it needs to populate.
        fooTableView.dataSource = self
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return fooData.count
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        // Access an instance of table view cell via an ID string. This is set in the storyboard > tableview prototype cell > Identifier field in the attributes right panel of XCode (see screenshot below)
        let cell: FooUITableViewCell = tableView.dequeueReusableCellWithIdentifier("FooUITableViewCellId", forIndexPath: indexPath) as! FooUITableViewCell // swiftlint:disable:this force_cast
        let listItem = fooData[indexPath.row]

        // Here, you can access the UITableViewCell view items directly, or do what I do here and call a function on the custom UITableViewCell class and have it take care of populating.
        cell.populateCell(listItem, position: indexPath.row)

        return cell
    }

    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        // TODO Do something here when a cell in your tableview selected.
    }

}
```

* Here is where you can access the UITableView storyboard ID:

![](/docs/images/tableview_cell_storyboard_id.png)

* Custom TableViewCell class:

```
protocol FooUITableViewCellDelegate {
    func profileImagePressed(data: FooModel, position: Int)
}

class FooUITableViewCell: UITableViewCell {

    @IBOutlet weak var profileImageView: UIImageView!

    private var fooData: FooModel!
    private var position: Int!

    var delegate: FooUITableViewCellDelegate?

    func populateCell(data: FooModel, position: Int) {
        self.fooData = data
        self.position = position

        // Populate imageview here.
    }

    @IBAction func profileImagePressed(sender: AnyObject) {
        delegate?.profileImagePressed(fooData, position: position)
    }

}

```

# Empty view for TableView

I like to use a library [DZEmptyDataSet](https://github.com/dzenbot/DZNEmptyDataSet) for taking care of my TableView empty view.

```
import UIKit
import DZNEmptyDataSet <----- import

class FooViewController: BaseUIViewController, UITableViewDelegate, UITableViewDataSource, DZNEmptyDataSetSource, DZNEmptyDataSetDelegate {  <---- add couple new protocols

    @IBOutlet weak var fooTableView: UITableView!

    override func viewDidLoad() {
        super.viewDidLoad()

        fooTableView.delegate = self
        fooTableView.dataSource = self
        fooTableView.emptyDataSetSource = self    <------ set data source
        fooTableView.emptyDataSetDelegate = self  <------ set delegate
    }

    ...

    func imageForEmptyDataSet(scrollView: UIScrollView!) -> UIImage! { <------ image used for empty view.
        return UIImage(named: "placeholder")
    }

    func titleForEmptyDataSet(scrollView: UIScrollView!) -> NSAttributedString! {  <----- title used for empty view.
        var message: String = "Loading data..."  <---- What I usually do, is set the string default to a loading state.

        if let _ = processesApiResponse { <----- then, if the data is not nil...we set it to empty state instead.
            message = "You don't have any data."
        }

        return NSAttributedString(string: message) <---- it is a NSAttributedString which means you can style font, color, size, alignment, etc.
    }        

    func descriptionForEmptyDataSet(scrollView: UIScrollView!) -> NSAttributedString! { <--- if you want more then a title, description can be added too.
        var text = "Description here below the title."

        let paragraphyStyle = NSMutableParagraphStyle()
        paragraphyStyle.lineBreakMode = NSLineBreakMode.ByWordWrapping
        paragraphyStyle.alignment = NSTextAlignment.Center

        let attributes = [NSParagraphStyleAttributeName: paragraphyStyle, NSForegroundColorAttributeName: UIColor.flatBlackColor()]

        return NSAttributedString(string: text, attributes: attributes)
    }    

    // You can even add a button to the empty view. Text or image.
    func buttonTitleForEmptyDataSet(scrollView: UIScrollView!, forState state: UIControlState) -> NSAttributedString! {

    }

    func buttonImageForEmptyDataSet(scrollView: UIScrollView!, forState state: UIControlState) -> UIImage! {

    }

}

```

These functions, at least 1 is required but not all of them. Don't want an image or description? No problem.

# Dynamic height header

* In Storyboard, drag a UIView above your prototype cells. This will be your header view (access via `tableview.tableHeaderView`). Add auto layout constraints as you always do to make some views specify a height, some don't to indicate dynamic height.

* In ViewController:

```
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()

    sizeHeaderToFit(answersTableView)
}

func sizeHeaderToFit(tableView: UITableView) {
    if let headerView = tableView.tableHeaderView {
        let height = headerView.systemLayoutSizeFittingSize(UILayoutFittingCompressedSize).height
        var frame = headerView.frame
        frame.size.height = height
        headerView.frame = frame
        tableView.tableHeaderView = headerView
        headerView.setNeedsLayout()
        headerView.layoutIfNeeded()
    }
}

```

# Full height, no scroll tableview

TODO needs more work on it.

If you want to add a tableview inside of a tableview, or you want to add a tableview inside of a scrollview, this is for you.

* In Interface Builder, create a tableview (or scrollview) and then in each cell, add another tableview. We will make the inner tableview change it's height to full height.

For the inner tableview, make sure to disable scrolling in IB.

* Create this tableview class:

```
import Foundation
import UIKit

class NoScrollTableView: DynamicCellHeightTableView {

    override var contentSize: CGSize {
        didSet {
            self.invalidateIntrinsicContentSize()
        }
    }

    override func intrinsicContentSize() -> CGSize {
        self.layoutIfNeeded()

        return CGSize(width: UIViewNoIntrinsicMetric, height: contentSize.height)
    }

}
```

And set `NoScrollTableView` as the inner tableview's UITableView class in IB.

* Create this tableview class:

```
import UIKit
import Foundation

@IBDesignable
class DynamicCellHeightTableView: UITableView {

    override func drawRect(rect: CGRect) {
        rowHeight = UITableViewAutomaticDimension
        estimatedRowHeight = 44.0
    }

}
```

And set `DynamicCellHeightTableView` as the outer tableview's class in IB (if using ScrollView don't set this. Just create it.)

*

# TableView results don't show on screen until you scroll

Sometimes your tableview might behave incorrectly where you have data, you set your tableview delegate and data source, then launch the app. The tableview is on the screen, but showing blank until you scroll where it *instantly* shows the tablview contents.

Fix with a simple reloadData call:

```
fooTableView.dataSource = self
fooTableView.delegate = self

fooTableView.reloadData()  // <------ will load data without "scroll" need. 
```
