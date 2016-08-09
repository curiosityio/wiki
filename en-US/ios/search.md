---
name: Search
---

To implement search in your app, you can use a SearchBar directly in your UI via interface builder or you can use a SearchController which has a SearchBar built in. There are pros and cons to both and times to use each:

Why use SearchController:

* Good UI for performing a search on an API. SearchController has this animation built in like the Apple contacts app that makes it seem like you are going to a different screen. Separates the "search" from viewing just a TableView of data.

Why use SearchBar:

* SearchBar is good for filtering. If your TableView has a set of data, using SearchBar is a view and that is it. Perfect for performing filtering on an existing data set. You stay in the TableView screen.

# SearchBar

How to get a SearchBar in your app:
*Thanks to [this guide](https://github.com/codepath/ios_guides/wiki/Search-Bar-Guide) for the help understanding search better. The example here is from this guide.*

* Add a SearchBar to your UI via interface builder.

* Add some code to your ViewController the SearchBar lives in:

```
class ViewController: UIViewController, UITableViewDataSource, UISearchBarDelegate {
    @IBOutlet weak var tableView: UITableView!
    @IBOutlet weak var searchBar: UISearchBar!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    override func viewDidLoad() {
        super.viewDidLoad()

        tableView.dataSource = self
        searchBar.delegate = self
        filteredData = data
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell

        cell.textLabel?.text = filteredData[indexPath.row]

        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return filteredData.count
    }

    func searchBar(searchBar: UISearchBar, textDidChange searchText: String) {
        filteredData = searchText.isEmpty ? data : data.filter({(dataString: String) -> Bool in
            return dataString.rangeOfString(searchText, options: .CaseInsensitiveSearch) != nil
        })
    }

    // This method updates filteredData based on the text in the Search Box
    func searchBar(searchBar: UISearchBar, textDidChange searchText: String) {
        // When there is no text, filteredData is the same as the original data
        if searchText.isEmpty {
            filteredData = data
        } else {
            // The user has entered text into the search box
            // Use the filter method to iterate over all items in the data array
            // For each item, return true if the item should be included and false if the
            // item should NOT be included
            filteredData = data.filter({(dataItem: String) -> Bool in
                // If dataItem matches the searchText, return true to include it
                if dataItem.rangeOfString(searchText, options: .CaseInsensitiveSearch) != nil {
                    return true
                } else {
                    return false
                }
            })
        }
        tableView.reloadData()
    }

    // show the cancel button to the right of the searchbar.
    func searchBarTextDidBeginEditing(searchBar: UISearchBar) {
        self.searchBar.showsCancelButton = true
    }

    // when cancel button is pressed.
    func searchBarCancelButtonClicked(searchBar: UISearchBar) {
        searchBar.showsCancelButton = false
        searchBar.text = ""
        searchBar.resignFirstResponder() // hide keyboard. ***NOTE: When you hide keyboard, make sure to hide the cancel button too. If not, the button loses focus and your user must press the cancel button twice in order to actually hit the cancel button. This will confuse your users.***
    }
}
```

This is what the above code looks like:

![](/docs/images/ios_searchbar_demo.gif)

Notice that the search results are displayed in the same table, and there is no presentation of a separate search interface.

The [SearchBarDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBarDelegate_Protocol/) has many functions you can override. Dealing with the cancel button, text change listeners for the searchbar.

# SearchController

*Thanks to [this guide](https://github.com/codepath/ios_guides/wiki/Search-Bar-Guide) for the help understanding search better. The example here is from this guide.*

SearchController is available iOS 8+. There was an old SearchDisplayController but that is deprecated now.

Add a SearchController to your code:

* Do **not** add a searchbar to your UI via interface builder. SearchController has a searchbar built in that you must use. If you already have one, remove it. *Note: Adding a SearchBar to TableView as we are doing below will add a 1px header border to the SearchBar. This results in there being a 1px view between your navigationbar and searchbar of your TableView. In Interface Builder, in your layout constraints for the TableView, make the top constraint be -1px to the superview. This will hide that 1px.*

* Here is some code for a ViewController you want to show the SearchController in:

```
class ViewController: UIViewController, UITableViewDataSource, UISearchResultsUpdating, UISearchBarDelegate { <----- Add UISearchResultsUpdating. If you are interacting with the searchbar (explained below in viewDidLoad) then also add UISearchBarDelegate
    @IBOutlet weak var tableView: UITableView!

    let data = ["New York, NY", "Los Angeles, CA", "Chicago, IL", "Houston, TX",
        "Philadelphia, PA", "Phoenix, AZ", "San Diego, CA", "San Antonio, TX",
        "Dallas, TX", "Detroit, MI", "San Jose, CA", "Indianapolis, IN",
        "Jacksonville, FL", "San Francisco, CA", "Columbus, OH", "Austin, TX",
        "Memphis, TN", "Baltimore, MD", "Charlotte, ND", "Fort Worth, TX"]

    var filteredData: [String]!

    var searchController: UISearchController!

    override func viewDidLoad() {
        super.viewDidLoad()

        tableView.dataSource = self
        filteredData = data

        // Initializing with searchResultsController set to nil means that
        // searchController will use this view controller to display the search results
        // If you have a tableview in your ViewController already (here we do) then use nil such as below.
        searchController = UISearchController(searchResultsController: nil)
        searchController.searchResultsUpdater = self

        // If we are using this same view controller to present the results
        // dimming it out wouldn't make sense. Should probably only set
        // this to yes if using another controller to display the search results.
        searchController.dimsBackgroundDuringPresentation = false

        searchController.searchBar.sizeToFit()
        tableView.tableHeaderView = searchController.searchBar

        // Sets this view controller as presenting view controller for the search interface
        definesPresentationContext = true

        // you can access the searchBar directly and change some settings on it.
        searchController.searchBar.delegate = self <---- If you are interacting with an API for search, I recommend using the searchbar delegate as you can watch for keyboard search press which will trigger a search.
        searchController.searchBar.barTintColor = UIColor.redColor()
        searchController.searchBar.tintColor = UIColor.lightGrayColor()

        // Add this to fix statusbar of device going to transparent when going into search mode with searchController. [See this](https://stackoverflow.com/questions/23124853/uitableview-content-overlaps-status-bar-when-uisearchbar-is-active) to understand problem.
        extendedLayoutIncludesOpaqueBars = true
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("TableCell") as UITableViewCell

        cell.textLabel?.text = filteredData[indexPath.row]

        return cell
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return filteredData.count
    }

    // IF you are interacting with an API, ignore this. This function triggers each and every time user enters data into keyboard.
    func updateSearchResultsForSearchController(searchController: UISearchController) {
        if let searchText = searchController.searchBar.text {
            filteredData = searchText.isEmpty ? data : data.filter({(dataString: String) -> Bool in
                return dataString.rangeOfString(searchText, options: .CaseInsensitiveSearch) != nil
            })

            tableView.reloadData()
        }
    }

    // For APIs, it is best to ignore the above and instead use this function:
    func searchBarSearchButtonClicked(searchBar: UISearchBar) {
        if searchBar.text?.characters.count <= 0 {
            searchBarCancelButtonClicked(searchBar)
        } else {
            // call API, get data, populate tableview.
        }
    }
}
```
