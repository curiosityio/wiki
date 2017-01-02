---
name: ViewPager
---

# Detect when user is dragging ViewPager to scroll to next page.

```
fooViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
    @Override public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
    }
    @Override public void onPageSelected(int position) {
        // Indicates when user has swiped to a new page. Position is zero indexed and indicates what page user is viewing.
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

# Dynamically change number of pages in viewpager

You may want to dynamically add more and more fragments to a viewpager.

This all depends on how you are returning from `getCount()` in the ViewPager adapter. One easy way to change it to is return back the value of a variable you can edit dynamically:

```
class FooViewPagerAdapter(val context: Context, fragmentManager: FragmentManager, val barData: BarData, var numberPages: Int) : FragmentStatePagerAdapter(fragmentManager) {

    override fun getItem(position: Int): Fragment {
        return BarChildFragment.Companion.newInstance(barData, position)
    }

    override fun getCount(): Int {
        return numberPages
    }

}
```

Now anytime you want to dynamically change the number of pages, pretty simple:

```
viewPagerAdapter?.numberPages = 5
viewPagerAdapter?.notifyDataSetChanged()
```

# Get current ViewPager index position

```
mViewPager.getCurrentItem()
```

# Parent fragment get current fragment shown to user

Inside of the parent fragment, use the following code anywhere you would like to get the currently shown fragment.

```
childFragmentManager.fragments[view!!.viewpager_id_here]
```

You can then cast it and do whatever you need with it:

```
(childFragmentManager.fragments[view!!.viewpager_id_here.currentItem] as FooPagerFragment).doSomething()
```
