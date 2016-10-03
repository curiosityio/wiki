---
name: Collection View
---

# Collection View 2 cells dynamic width cells

In your ViewController delegate for the CollectionView:

```
func collectionView(collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAtIndexPath indexPath: NSIndexPath) -> CGSize {
    let cellPadding = CGFloat(10.0)
    let numberColumns = CGFloat(2.0)
    let cellHeightWidth = answersCollectionView.frame.width / numberColumns - cellPadding

    return CGSize(width: cellHeightWidth, height: cellHeightWidth)
}
```
