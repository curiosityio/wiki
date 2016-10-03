---
name: ScrollView
---

# Setup a ScrollView with autolayout in storyboard

[This YouTube video is short, sweet, explains it well.](https://www.youtube.com/watch?v=RV7vz4WSy7w)

*Note:* The YouTube video is great, but 1 difference. He sets up his layout scructure in this format:

```
UIView <-- view controller's view.
  |
  --- ScrollView
          |
          --- UIView
                 |
                 --- UIView
                       |
                       --- UILabel <--- finally, your subviews you want to add to your scrollview.
```

All you really need is:

```
UIView <-- view controller's view.
  |
  --- ScrollView
          |
           --- UIView
                 |
                  --- UILabel <--- finally, your subviews you want to add to your scrollview.
```

You need a view inside of the scrollview with autolayout constraints which is the magic behind autolayout scrollview getting it to work. Then, you may create your layout as usual.

Scrollviews  are good for dynamic height views, such as a label for example. The secret to autolayout in a scrollview is to give height attributes to views that are set height and don't give height attribute to views that are dynamic. Also, make sure to make all of your subviews of UIView pin to the top, bottom, left, right of the views or you will be an error. 
