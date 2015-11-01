---
layout: post
permalink: mastering-coordinator
title: Mastering the Coordinator Layout
tags:
- Design
---

In the [Google I/O 15](https://www.youtube.com/watch?v=7V-fIGMDsmE), Google released a [new support library](http://android-developers.blogspot.com.es/2015/05/android-design-support-library.html) which implements several components closely related with the [Material Design's spec](https://www.google.com/design/spec/material-design/introduction.html), among these components you can find new _ViewGroups_ like the `AppbarLayout`, `CollapsingToolbarLayout` and `CoordinatorLayout`.

Well combined and configured these _Viewgroups_ can be very powerful, therefore I have decided to write a post with some configurations and tips. 

<br>
## CoordinatorLayout

As its name suggests, the goal and philosophy of this _ViewGroup_ is to **coordinate** the views that are inside it.

Consider the following picture:

<br>
![](http://androcode.es/wp-content/uploads/2015/10/simple_coordinator.gif)

<br>
In this example you can see how the views are coordinated with each other, with a glance, we can see how some _Views_ **depend** on other. (we'll talk about this later).

This would be one of the simplest structures using the `CoordinatorLayout`:
<br>

```xml
<?xml version="1.0" encoding="utf-8"?>

<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/background_light"
    android:fitsSystemWindows="true"
    >

    <android.support.design.widget.AppBarLayout
        android:id="@+id/main.appbar"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:fitsSystemWindows="true"
        >

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/main.collapsing"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            android:fitsSystemWindows="true"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleMarginStart="48dp"
            app:expandedTitleMarginEnd="64dp"
            >

            <ImageView
                android:id="@+id/main.backdrop"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                android:fitsSystemWindows="true"
                android:src="@drawable/material_flat"
                app:layout_collapseMode="parallax"
                />

            <android.support.v7.widget.Toolbar
                android:id="@+id/main.toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                app:layout_collapseMode="pin"
                />
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>

    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        >

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textSize="20sp"
            android:lineSpacingExtra="8dp"
            android:text="@string/lorem"
            android:padding="@dimen/activity_horizontal_margin"
            />
    </android.support.v4.widget.NestedScrollView>

    <android.support.design.widget.FloatingActionButton
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:layout_margin="@dimen/activity_horizontal_margin"
        android:src="@drawable/ic_comment_24dp"
        app:layout_anchor="@id/main.appbar"
        app:layout_anchorGravity="bottom|right|end"
        />
</android.support.design.widget.CoordinatorLayout>
```

Consider the skeleton of that layout. The `CoordinatorLayout` has only three childs: an `AppbarLayout`,  an _scrolleable_ view and an anchored `FloatingActionBar`.

```xml
<CoordinatorLayout>
    <AppbarLayout/>
    <scrollableView/>
    <FloatingActionButton/>
</CoordinatorLayout>
```


<br>
## AppBarLayout

Basically, an `AppBarLayout` is a `LinearLayout` with steroids, their children are placed vertically, with certain parameters the children can manage their behavior when the content is scrolled.

It may sound confusing at first, so if I think a picture is worth a thousand words, a .gif, is even better:

<br>
![](http://androcode.es/wp-content/uploads/2015/10/2015-10-27-03_51_37.gif)
<br>
The `AppBarLayout` in this case is the blue view, placed under the collapsing image, it contains a `Toolbar`, a `LinearLayout` with title and subtitle and a `TabLayout` with some tabs.

We can manage the behavior of  direct childs in an `AppbarLayout` with the parameter: `layout_scrollFlags`. The value: `scroll` in this case it's present in almost all views, if it wasn't specified in any child then the childs of the `AppbarLayout` will remain static allowing the _scrollable_ content slide behind it.

With the value: `snap`, we avoid fall into _mid-animation-states_, this means that the animations will always hide or expand all the height of its view.

The `LinearLayout` which contains the title and subtitle will be shown always that the user scrolls up, (`enterAlways` value), and the `TabLayout` will be always visible because we don't have any flag on it.

As you can see the real power of an `AppbarLayout` is caused by the proper management of the different scroll flags in their views.

```xml
<AppBarLayout>
    <CollapsingToolbarLayout
        app:layout_scrollFlags="scroll|snap"
        />

    <Toolbar
        app:layout_scrollFlags="scroll|snap"
        />

    <LinearLayout
        android:id="+id/title_container"
        app:layout_scrollFlags="scroll|enterAlways"
        />

    <TabLayout /> <!-- no flags -->
</AppBarLayout>
```

These are all available parameters acording [Google Developers docs](https://developer.android.com/intl/es/reference/android/support/design/widget/AppBarLayout.LayoutParams.html#CONSTANTS). Anyway, my recommendation is to always play by example. There are some Github repositories with these implementations at the end of this article.

### AppbarLayout flags
                                                                         
`SCROLL_FLAG_ENTER_ALWAYS`: When entering (scrolling on screen) the view will scroll on any downwards scroll event, regardless of whether the scrolling view is also scrolling. 

`SCROLL_FLAG_ENTER_ALWAYS_COLLAPSED`: An additional flag for 'enterAlways' which modifies the returning view to only initially scroll back to it's collapsed height.                      

`SCROLL_FLAG_EXIT_UNTIL_COLLAPSED`: When exiting (scrolling off screen) the view will be scrolled until it is 'collapsed'.                              

`SCROLL_FLAG_SCROLL`: The view will be scroll in direct relation to scroll events.                                                                                        
`SCROLL_FLAG_SNAP`: Upon a scroll ending, if the view is only partially visible then it will be snapped and scrolled to it's closest edge.                       
<br>
## CoordinatorLayout Behaviors

Let's do a little test, go to Android Studio (>= 1.4) and create a project with the template: _Scrolling Activity_, without touching anything, we compile it and this is what we find:

![](http://androcode.es/wp-content/uploads/2015/10/2015-10-27-03_59_27.gif)

<br>
If we review the generated code, neither layouts nor java classes won't have anything related with the Fab's _scale_ animation on scroll. Why?

 The answer lies in the `FloatingActionButton` source code, since Android Studio v1.2 includes a java decompiler inside it, with `ctrl/cmd + click` we are able to check the source and see what happens:

```java
/*
 * Copyright (C) 2015 The Android Open Source Project
 *
 *  Floating action buttons are used for a
 *  special type of promoted action. 
 *  They are distinguished by a circled icon 
 *  floating above the UI and have special motion behaviors 
 *  related to morphing, launching, and the transferring anchor point.
 * 
 *  blah.. blah.. 
 */
@CoordinatorLayout.DefaultBehavior(
    FloatingActionButton.Behavior.class)
public class FloatingActionButton extends ImageButton {
    ...

    public static class Behavior 
        extends CoordinatorLayout.Behavior<FloatingActionButton> {

        private boolean updateFabVisibility(
           CoordinatorLayout parent, AppBarLayout appBarLayout, 
           FloatingActionButton child {

           if (a long condition) {
                // If the anchor's bottom is below the seam, 
                // we'll animate our FAB out
                child.hide();
            } else {
                // Else, we'll animate our FAB back in
                child.show();
            }
        }
    }

    ...
}
```

Who is in charge of that scale animation is a new element introduced with the design library called `Behavior`. In this case a `CoordinatorLayout.Behavior<FloatingAcctionButton>`, which depending on some factors including the scroll, shows the FAB or not, interesting, right?.

###SwipeDismissBehavior

Keep diving into the code, if you look inside the _widget_ package of the **design support library**, we'll find a public class called: `SwipeDismissBehavior`. With this new `Behavior` we can very easily implement the functionality of _swipe to dismiss_ in our layouts with a `CoordinatorLayout`:

![](http://androcode.es/wp-content/uploads/2015/10/hammerheadLMY48Msaulmm10242015161844.gif)


```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_swipe_behavior);
    mCardView = (CardView) findViewById(R.id.swype_card);

    final SwipeDismissBehavior<CardView> swipe 
        = new SwipeDismissBehavior();

        swipe.setSwipeDirection(
            SwipeDismissBehavior.SWIPE_DIRECTION_ANY);

        swipe.setListener(
            new SwipeDismissBehavior.OnDismissListener() {
            @Override public void onDismiss(View view) {
                Toast.makeText(SwipeBehaviorExampleActivity.this,
                    "Card swiped !!", Toast.LENGTH_SHORT).show();
            }

            @Override 
            public void onDragStateChanged(int state) {}
        });

        LayoutParams coordinatorParams = 
            (LayoutParams) mCardView.getLayoutParams();
        
        coordinatorParams.setBehavior(swipe);
    }
```

<br>

## Custom Behaviors

To create a custom `Behavior` It isn't as difficult as it may seem, to begin we must take into account two core elements: **child** and **dependency**.

<br>
![](http://androcode.es/wp-content/uploads/2015/10/Screen-Shot-2015-10-27-at-04.42.37-e1445917457348.png)
<br>

### Childs and dependencies

The **child** is the view that enhances behavior, **dependency** who will serve as a trigger to interact with the _child_ element. See this example, the **child** would be the `ImageView` and the **dependency** would be the `Toolbar`, in that way, if the `Toolbar` moves, the `ImageView` will move too.

<br>

![](http://androcode.es/wp-content/uploads/2015/10/2015-10-27-04_30_50.gif)

<br>

Now that we have defined the concepts we can speak of implementation, the first step is to extend of: `CoordinatorLayout.Behavior<T>`, been `T` the class that belongs the view that interests us coordinate, in this case an _ImageView_, after that we must override these methods:

- layoutDependsOn
- onDependentViewChanged

The method: `layoutDependsOn` will be called every time that something happens in the layout, what we must do to return `true` once we identify the dependency, in the example, this method is automatically fired when the user scrolls (because the `Toolbar` will move), in that way we can make our child sight react accordingly.

```java
   @Override
   public boolean layoutDependsOn(       
      CoordinatorLayout parent, 
      CircleImageView, child, 
      View dependency) {
   
      return dependency instanceof Toolbar; 
  } 
```

Whenever `layoutDependsOn` returns `true` the second `onDependentViewChanged` will be called. Here is where we must to implement our animations, translations or movements always related with the provided dependency.

```java
   public boolean onDependentViewChanged( 
      CoordinatorLayout parent, 
      CircleImageView avatar, 
      View dependency) { 
      
      modifyAvatarDependingDependencyState(avatar, dependency);
   }

   private void modifyAvatarDependingDependencyState(
    CircleImageView avatar, View dependency) {
        //  avatar.setY(dependency.getY());
        //  avatar.setBlahBlat(dependency.blah / blah);
    } 
```

All together:

```java
 public static class AvatarImageBehavior 
   extends  CoordinatorLayout.Behavior<CircleImageView> {

   @Override
   public boolean layoutDependsOn( 
    CoordinatorLayout parent, 
    CircleImageView, child, 
    View dependency) {
   
       return dependency instanceof Toolbar; 
  }      

  public boolean onDependentViewChanged(       
    CoordinatorLayout parent, 
    CircleImageView avatar, 
    View dependency) { 
      modifyAvatarDependingDependencyState(avatar, dependency);
   }

  private void modifyAvatarDependingDependencyState(
    CircleImageView avatar, View dependency) {
        //  avatar.setY(dependency.getY());
        //  avatar.setBlahBlah(dependency.blah / blah);
    }    
}
```

## Resources

- [Coordinator Behavior Example](https://github.com/saulmm/CoordinatorBehaviorExample) - Github
- [Coordinator Examples](https://github.com/saulmm/CoordinatorExamples) - Github
- [Introduction to coordinator layout on Android](https://lab.getbase.com/introduction-to-coordinator-layout-on-android/) - Grzesiek Gajewski
