---
name: ViewPager
---

# Detect when user is dragging ViewPager to scroll to next page.

```
fooViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
    @Override public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
    }
    @Override public void onPageSelected(int position) {
    }
    @Override public void onPageScrollStateChanged(int state) {
        if (state == ViewPager.SCROLL_STATE_DRAGGING) {
            // This gets triggered one time when user starts to drag the ViewPager to scroll to next page.
        } else if (state == ViewPager.SCROLL_STATE_IDLE) {
            // Indicates that the pager is in an idle, settled state. The current page is fully in view and no animation is in progress.
        } else if (state == ViewPager.SCROLL_STATE_SETTLING) {
            // Indicates that the pager is in the process of settling to a final position.
        }
    }
});
```
