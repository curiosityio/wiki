---
name: AutoLayout
---

# Recipes

## What can I do if I want to add a padding to my auto resizing UITableViewCell that contains a UIStackView as the root view and the views inside of the UIStackView have intrinsic content sizes?

Add an inset to the root stackview. *Also, don't forget to set the hugging and compression properties of the UIStackView's subviews*

```swift
rootStackView.snp.makeConstraints { (make) in
    make.edges.equalToSuperview().inset(20)
}
```

Above, the UIStackView will have a padding of 20. 