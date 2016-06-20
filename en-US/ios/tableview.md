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
