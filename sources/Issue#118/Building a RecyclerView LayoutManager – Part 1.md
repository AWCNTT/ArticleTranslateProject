---
layout: post
title: "Building a RecyclerView LayoutManager – Part 1"
date: 2014-09-12 10:25
comments: true
categories: RecyclerView RecyclerViewItemAnimators
---

By now, if you’re an Android developer paying any attention, you’ve at least heard of RecyclerView; a new component that will be added to the support library to facilitate custom implementations of high-performance view collections by facilitating view recycling. Others have already done a remarkable job describing the basics of how to use RecyclerView with the built-in pieces already provided, including item animations. So rather than dive into all that again, here are some resources to get you up to speed:

* A First Glance at Android’s RecyclerView
* The new TwoWayView
* RecyclerViewItemAnimators

In this series of posts, we will be focused on the low-level details involved in building your own LayoutManager implementation, to do something a bit more complex than a simple vertical or horizontal scrolling list.

> Before going any further, a warning is in order. The LayoutManager API allows powerful and complex layout recycling because it doesn’t do much for you; these implementations involve a fair amount of code you have to write yourself. As with any project involving custom views, don’t get caught in a trap of over-optimizing or over-generalizing your code. Build the features you need for the application use case you’re concerned with.

## RecyclerView Playground

All the code snippets in this post series are taken from the [RecyclerView Playground sample](https://github.com/devunwired/recyclerview-playground) that I have posted on GitHub. This sample application includes examples of various aspects of working with RecyclerView, from creating simple lists, to custom LayoutManagers.

Code for this post is drawn from the `FixedGridLayoutManager` example; a two-dimensional grid layout with full scrolling in both the horizontal and vertical directions.

>The support library also has a sample that includes a custom LayoutManager; it is essentially a custom implementation of a vertical linear list: [SDK_PATH]/extras/android/compatibility/samples/Support7Demos/src/com/example/android/supportv7/widget/RecyclerViewActivity.java

>Also, while much of Android “L” and the new support libraries may not yet be in AOSP, the RecyclerView support artifact ships with a sources JAR you can view inside your environment: [SDK_PATH]/extras/android/m2repository/com/android/support/recyclerview-v7/21.0.0-rc1/recyclerview-v7-21.0.0-rc1-sources.jar

## The Recycler

First, a bit of insight into how the API is structured. Your LayoutManager is given access to a `Recycler` instance at key points in the process when you might need to recycle old views, or obtain new views from a potentially recycled previous child.

The Recycler also removes the need to directly access the view’s current adapter implementation. When your LayoutManager requires a new child view, simply call `getViewForPosition()` and the Recycler will return the view with the appropriate data already bound. The Recycler takes care of determining whether a new view must be created, or if an existing scrapped view gets reused. Your responsibility, inside your LayoutManager, is to ensure that views which are no longer visible get passed to the Recycler in a timely manner; this will keep the Recycler from creating more view objects than is necessary.

### Detach vs. Remove

There are two ways to handle existing child views during a layout update: detach and remove. Detach is meant to be a lightweight operation for reordering views. Views that are detached are expected to be re-attached before your code returns. This can be used to modify the indices of attached child views without re-binding or re-creating those views through the Recycler.
Remove is meant for views that are no longer needed. Any view that is permanently removed should be placed in the Recycler for later re-use, but the API does not enforce this. It is up to you whether the views you remove also get recycled.

### Scrap vs. Recycle

Recycler has a two-level view caching system: the scrap heap and the recycle pool. The scrap heap represents a lighter weight collection where views can be returned to the LayoutManager directly without passing through the adapter again. Views are typically placed here when they are temporarily being detached, but will be re-used within the same layout pass. The recycle pool consists of views that are assumed to have incorrect data (data from a different position), so they will always be passed through the adapter to have data re-bound before they are returned to the LayoutManager.

When attempting to supply the LayoutManager with a new view, a Recycler will first check the scrap heap for a matching position/id; if one exists, it will be returned without re-binding to the adapter data. If no matching view is found, the Recycler will instead pull a suitable view from the recycle pool and bind the necessary data to it from the adapter (i.e. `RecyclerView.Adapter.bindViewHolder(`) is invoked) before returning it. In cases where no valid views exist in the recycle pool, a new view will be created instead (i.e. `RecyclerView.Adapter.createViewHolder()` is invoked) before being bound, and returned.

### Rule of Thumb

The LayoutManager API lets you do pretty much all of these tasks independently if you wish, so the possible combinations can be a bit numerous. In general, use `detachAndScrapView()` for views you want to temporarily reorganize and expect to re-attach inside the same layout pass. Use `removeAndRecycleView()` for views you no longer need based on the current layout.

## Building The Core

A LayoutManager implementation is responsible for attaching, measuring, and laying out all the child views it needs in real-time. As the user scrolls the view, it will be up to the layout manager to determine when new child views need to be added, and old views can be detached and scrapped.
You will need to override and implement the following method to create a minimum viable LayoutManager.

### generateDefaultLayoutParams()

This is actually the only required override to get your LayoutManager to compile. The implementation is pretty straightforward, just return a new instance of the `RecyclerView.LayoutParams` that you want applied by default to all the child views returned from the Recycler. These parameters will be applied to each child before they return from `getViewForPosition()`.

```
@Override
public RecyclerView.LayoutParams generateDefaultLayoutParams() {
    return new RecyclerView.LayoutParams(
        RecyclerView.LayoutParams.WRAP_CONTENT,
        RecyclerView.LayoutParams.WRAP_CONTENT);
}
```

### onLayoutChildren()

This is the main entry point into your LayoutManager. This method will be called when the view needs to do an initial layout, and again whenever the adapter data set changes (or the entire adapter is swapped out). **This method is NOT called for every possible change you need to make to the layout**. It is a good opportunity to reset the layout of child views for either an initial pass or a data set change.

In the next segment, we will look at how this can be used to do a fresh layout based on the currently visible elements when the adapter updates. For now, we’ll address it simply as the first pass for laying out child views. Below is a simplified version of what exists in the `FixedGridLayoutManager` example:

```
@Override
public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
    //Scrap measure one child
    View scrap = recycler.getViewForPosition(0);
    addView(scrap);
    measureChildWithMargins(scrap, 0, 0);

    /*
     * We make some assumptions in this code based on every child
     * view being the same size (i.e. a uniform grid). This allows
     * us to compute the following values up front because they
     * won't change.
     */
    mDecoratedChildWidth = getDecoratedMeasuredWidth(scrap);
    mDecoratedChildHeight = getDecoratedMeasuredHeight(scrap);
    detachAndScrapView(scrap, recycler);
    
    updateWindowSizing();
    int childLeft;
    int childTop;

    /*
     * Reset the visible and scroll positions
     */
    mFirstVisiblePosition = 0;
    childLeft = childTop = 0;
    
    //Clear all attached views into the recycle bin
    detachAndScrapAttachedViews(recycler);
    //Fill the grid for the initial layout of views
    fillGrid(DIRECTION_NONE, childLeft, childTop, recycler);
}
```

We do some bookkeeping and setup (this manager assumes all child views from the adapter will be the same size, for simplicity), and make sure all views that might have existed are in the scrap heap. I have abstracted the bulk of the work to a `fillGrid()` helper method for re-use. We will see shortly this method gets called a lot to update the visible views as scrolling occurs as well.

> Just as with a custom ViewGroup implementation, you are responsible for triggering measure and layout on each child view you get from the Recycler. None of this work is done directly by the APIs.

In general, the primary steps you would want to accomplish in a method like this are:

1. Check the current offset positions of all the attached views after the latest scroll event.
1. Determine if there are empty spaces created by scrolling where new views need to be added. Get them from the Recycler.
1. Determine if there are now existing views that are no longer visible. Remove them and place them in the Recycler.
1. Determine if the remaining views should be reorganized. It may be that the above changes require you modify the child indices of the views to better align with their adapter positions.

Notice the primary steps we’ve taken to fill the RecyclerView inside of `FixedGridLayoutManager.fillGrid()`. This manager orders positions right-to-left, wrapping when the max column count is reached:

1.Take inventory of the views we have currently. Detach them all so they can be re-attached again later.

```
SparseArray<View> viewCache = new SparseArray<View>(getChildCount());
//...
if (getChildCount() != 0) {
    //...
    //Cache all views by their existing position, before updating counts
    for (int i=0; i < getChildCount(); i++) {
        int position = positionOfIndex(i);
        final View child = getChildAt(i);
        viewCache.put(position, child);
    }
    //Temporarily detach all views.
    // Views we still need will be added back at the proper index.
    for (int i=0; i < viewCache.size(); i++) {
        detachView(viewCache.valueAt(i));
    }
}
```

2.Do measure/layout for each child view that is visible at the current moment. Views we already had are simply re-attached; new views are obtained from the Recycler.

```
for (int i = 0; i < getVisibleChildCount(); i++) {
    //...

    //Layout this position
    View view = viewCache.get(nextPosition);
    if (view == null) {
        /*
         * The Recycler will give us either a newly constructed view,
         * or a recycled view it has on-hand. In either case, the
         * view will already be fully bound to the data by the
         * adapter for us.
         */
        view = recycler.getViewForPosition(nextPosition);
        addView(view);

        /*
         * It is prudent to measure/layout each new view we
         * receive from the Recycler. We don't have to do
         * this for views we are just re-arranging.
         */
        measureChildWithMargins(view, 0, 0);
        layoutDecorated(view, leftOffset, topOffset,
                leftOffset + mDecoratedChildWidth,
                topOffset + mDecoratedChildHeight);
    } else {
        //Re-attach the cached view at its new index
        attachView(view);
        viewCache.remove(nextPosition);
    }

    //...
}
```

3.Finally, any views we detached in Step 1 that did not get re-attached are no longer visible. Relegate them all to the Recycler for use later on.

```
for (int i=0; i < viewCache.size(); i++) {
    recycler.recycleView(viewCache.valueAt(i));
}
```

As a side note, the reason we detach all the views and re-attach just those we still need is to preserve the order of child indices (i.e. the `getChildAt()` index) for each view. We expect that the visible views flow from top-left as `0` to bottom-right as `getChildCount()-1`. As we scroll in both directions and new children are attached, this ordering would become unreliable. We need this order to be preserved to best determine the location of each child at any point. In a simpler LayoutManager (like LinearLayoutManager) where child views can be easily inserted at each end of the list, this level of bookkeeping isn’t necessary.

### Adding User Interactivity

At this stage we have a very nice initial layout, but no way to move around. The whole point of a RecyclerView is to dynamically provide views as the user scrolls through a data set! A few more overrides will get us there.

### canScrollHorizontally() & canScrollVertically()

These methods are simple. Just return true if your view supports any scrolling at all in the given direction, and false if you want to ignore it.

```
@Override
public boolean canScrollVertically() {
    //We do allow scrolling
    return true;
}
```

### scrollHorizontallyBy() & scrollVerticallyBy()

Here you will implement the logic by which the content should shift. Scrolling and flinging touch logic is handled already by RecyclerView, so no messing with MotionEvents or GestureDetectors. You have three tasks inside of these methods:

1. Shift all child views by the appropriate distance (yes, you have to do this part yourself).
1. Determine if another fill is in order to add/remove views once we’ve shifted.
1. Return back the actual distance traveled. The framework uses this to know when you’ve hit an edge boundary.

In `FixedGridLayoutManager`, both methods are very similar. Here is the condensed implementation of scrolling vertically:

```
@Override
public int scrollVerticallyBy(int dy, RecyclerView.Recycler recycler,
        RecyclerView.State state) {

    if (getChildCount() == 0) {
        return 0;
    }

    //Take top measurements from the top-left child
    final View topView = getChildAt(0);
    //Take bottom measurements from the bottom-right child.
    final View bottomView = getChildAt(getChildCount()-1);

    //Optimize the case where the entire data set is too small to scroll
    int viewSpan = getDecoratedBottom(bottomView) - getDecoratedTop(topView);
    if (viewSpan <= getVerticalSpace()) {
        //We cannot scroll in either direction
        return 0;
    }

    int delta;
    int maxRowCount = getTotalRowCount();
    boolean topBoundReached = getFirstVisibleRow() == 0;
    boolean bottomBoundReached = getLastVisibleRow() >= maxRowCount;

    if (dy > 0) { // Contents are scrolling up
        //Check against bottom bound
        if (bottomBoundReached) {
            //If we've reached the last row, enforce limits
            int bottomOffset;
            if (rowOfIndex(getChildCount() - 1) >= (maxRowCount - 1)) {
                //We are truly at the bottom, determine how far
                bottomOffset = getVerticalSpace() - getDecoratedBottom(bottomView)
                        + getPaddingBottom();
            } else {
                /*
                 * Extra space added to account for allowing bottom space in the grid.
                 * This occurs when the overlap in the last row is not large enough to
                 * ensure that at least one element in that row isn't fully recycled.
                 */
                bottomOffset = getVerticalSpace() - (getDecoratedBottom(bottomView)
                        + mDecoratedChildHeight) + getPaddingBottom();
            }
            delta = Math.max(-dy, bottomOffset);
        } else {
            //No limits while the last row isn't visible
            delta = -dy;
        }
    } else { // Contents are scrolling down
        //Check against top bound
        if (topBoundReached) {
            int topOffset = -getDecoratedTop(topView) + getPaddingTop();
            delta = Math.min(-dy, topOffset);
        } else {
            delta = -dy;
        }
    }

    offsetChildrenVertical(delta);

    if (dy > 0) {
        if (getDecoratedBottom(topView) < 0 && !bottomBoundReached) {
            fillGrid(DIRECTION_DOWN, recycler);
         } else if (!bottomBoundReached) {
            fillGrid(DIRECTION_NONE, recycler);
         }
    } else {
        if (getDecoratedTop(topView) > 0 && !topBoundReached) {
            fillGrid(DIRECTION_UP, recycler);
        } else if (!topBoundReached) {
            fillGrid(DIRECTION_NONE, recycler);
        }
    }

    /*
     * Return value determines if a boundary has been reached
     * (for edge effects and flings). If returned value does not
     * match original delta (passed in), RecyclerView will draw
     * an edge effect.
     */
    return -delta;
}
```

Notice we are given the incremental scroll distance (dx/dy) to validate. The first portion of this method deals with determining whether scrolling the given distance (the sign gives the direction) would overscroll the edge of our content. If it would, we need to reduce how far the views are actually moved.

We must manually move the views ourselves inside of this method. The `offsetChildrenVertical()` and `offsetChildrenHorizontal()` methods assist us in applying the uniform translation. **If you don’t do this, your views won’t scroll**. After moving the views, we trigger another fill operation to swap views based on the direction we scrolled.

Finally, we return the actual shift value applied to the children. RecyclerView uses this value to determine when it should draw the edge effects you see on any scrolling content when the end is reached. Essentially, if the return value doesn’t exactly match the dx/dy passed in, expect some amount of edge glow to be drawn. **Also note that if you return a value with the incorrect sign, the framework’s math will take that also as a big change and you’ll get edge glows at the wrong time**.

In addition to drawing edge effects, this return value is also used to determine when to cancel flings. Returning the incorrect value here will disable your ability to fling the content as the framework will think you’ve prematurely hit an edge and abort the fling.

### Just Getting Warmed Up

At this point, we should have a base functional implementation. It’s missing some of the finer details, but scrolling and proper view recycling are in place. There’s a lot more to go in our discussion of building a custom LayoutManager. In the next segment, we will look at dealing with decorations, data set changes, and implementing position scrolling.

---


原文地址：[http://wiresareobsolete.com/2014/09/building-a-recyclerview-layoutmanager-part-1/](http://wiresareobsolete.com/2014/09/building-a-recyclerview-layoutmanager-part-1/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)