---
layout: post
permalink: a-useful-stack-on-android-3-compatibility

---
<br>
This is the third post of the series: '_A useful stack on Android_'.

In the [first part](http://saulmm.github.io/2015/02/02/A%20useful%20stack%20on%20android%20%231,%20architecture/) I have tried to define a modular and scalabe architecture, based on the pattern: [Model View Presenter (MVP)](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter).

The [second part](http://saulmm.github.io/a-useful-stack-on-android-2-user-interface/) has explained how to take small bites of [Material Design](http://www.google.com/design/spec/material-design/introduction.html) on the user interface, through colors, transitions, vectors, etc.

This third part is about **compatibility** , it is known that the fragmentation of Android is huge, versions, screen sizes, fetaures, etc. For that reason we go down a few versions from the inital version of the project (LLolipop) and we will support diferent screen sizes.

All this examples are available on **GitHub** - [https://github.com/saulmm/Material-Movies](https://github.com/saulmm/Material-Movies)

<br>
### Downgrading SDK Levels

I opted the level 16 of the Android SDK,  from the [Dashboards](https://developer.android.com/about/dashboards/index.html) published by Google to date, the number of devices that support versions from Jelly Bean is an appealing **86,8 %**.

![](http://androcode.es/wp-content/uploads/2015/03/Screen-Shot-2015-03-09-at-14.48.56-e1425909065199.png)

To give support to these versions, some minor changes are necessary, it is the case of the [transitions with shared elements](https://developer.android.com/reference/android/view/Window.html#setSharedElementEnterTransition(android.transition.Transition)), that were not introduced until the 21 version of the Android _framework_.

<br>
### Shared element transitions

When you press a film in the `MoviesActivity`, is checked if we have a version above or equal to _Lollipop_, if so, we may use the new API of transitions with shared elements (in this case the _poster_ of the film); If not, I apply an animation to achieve a similar effect.

_MoviesActivity_ 


```java
@Override
public void onClick(View v, int position, 
    float touchedX, float touchedY) {
    
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) 
        startSharedElementPosition(touchedView, position,
            movieDetailActivityIntent);

    else
        startDetailActivityAnimation(touchedView, (int) touchedX, 
            (int) touchedY, movieDetailActivityIntent);
}

```


Instead of having a shared element, which is moving between the two activities, in the `MovieDetailActivity`, I will scale the _poster_ of the film from the last point which the user has touched.

_MovieDetailActivity.java_ 

```java
@Override
public void onCreate(Bundle savedInstanceState) {
    
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) 
        configureEnterTransition ();

     else {

        mViewLastLocation = getIntent()
            .getIntArrayExtra("view_location");

        configureEnterAnimation ();
    }
}
```

```java
private void configureEnterAnimation() {
    
    GUIUtils.startScaleAnimationFromPivot(
        mViewLastLocation[0], mViewLastLocation[1],
        mObservableScrollView, new AnimatorAdapter() {

            @Override
            public void onAnimationEnd(Animator animation) {

                super.onAnimationEnd(animation);
                GUIUtils.showViewByScale(mFabButton);
            }
        }
    );

    animateElementsByScale();
}
```

_GuiUtils.java_

```java
public static void startScaleAnimationFromPivot (
    int pivotX, int pivotY, final View v,
    final AnimatorListener animatorListener) {

    final AccelerateDecelerateInterpolator interpolator =
        new AccelerateDecelerateInterpolator();

    v.setScaleY(SCALE_START_ANCHOR);
    v.setPivotX(pivotX);
    v.setPivotY(pivotY);

    v.getViewTreeObserver().addOnPreDrawListener(
        new OnPreDrawListener() {
            
            @Override
            public boolean onPreDraw() {

                v.getViewTreeObserver().removeOnPreDrawListener(this);

                ViewPropertyAnimator viewPropertyAnimator = 
                    v.animate()
                    .setInterpolator(interpolator)
                    .scaleY(1)
                    .setDuration(SCALE_DELAY);

                if (animatorListener != null)
                    viewPropertyAnimator.setListener(
                        animatorListener);

                viewPropertyAnimator.start();
                return true;
            }
        });
    }
```

Result:

![](https://7bf7641794e4cf95af1861265aa16d65ab2050ee.googledrive.com/host/0B62SZ3WRM2R2NHFzSFh6NnJZemM)

<br>
### VectorDrawables & Slide transition

Another aspect to make compatible are the `VectorDrawables`, they were not introduced until version 21 of the sdk, (_LLolipop_), instead, I show a little animation that scales and rotates the star, that is made by a  `ViewPropertyAnimator`.

The Animation: [CircularReveal](https://developer.android.com/reference/android/view/ViewAnimationUtils.html#createCircularReveal (android.view.View, int, int, float, float)), is not available until _LLolipop_, so in the same way that with transitions, I scale a view from the last point touched.

_MovieDetailActivity.java_

```java
@Override
public void showConfirmationView() {

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP)
        GUIUtils.showViewByRevealEffect(mConfirmationContainer,
            mFabButton, GUIUtils.getWindowWidth(this));

     else
        GUIUtils.startScaleAnimationFromPivot(
            (int) mFabButton.getX(),(int) mFabButton.getY(),
            mConfirmationContainer, null);

    animateConfirmationView();
    startClosingConfirmationView();
}
```

_MovieDetailActivity.java_

```java
@Override
public void animateConfirmationView() {

    Drawable drawable = mConfirmationView.getDrawable();

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) 
        if (drawable instanceof Animatable)
            ((Animatable) drawable).start();

    else 
        mConfirmationView.startAnimation(
            AnimationUtils.loadAnimation(this,
                R.anim.appear_rotate));
    }
}
```

<br>
Result:

![](http://androcode.es/wp-content/uploads/2015/03/scale.gif)


<br>
## Supporting different screen sizes

If something is known, android is for being able to operate in a multitude of devices, the variation between the size and quality of the different screens is very significant, to make sure everything is displayed properly in a 4" phone and a tablet of 10-12" must follow a few guides.

![](http://androcode.es/wp-content/uploads/2015/03/family2.png)

<br>
## AutofitRecyclerView

The films are arranged in the form of grid by a [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html) with a [GridLayoutManager](https://developer.android.com/reference/android/support/v7/widget/GridLayoutManager.html). Google provides a [constructor](https://developer.android.com/reference/android/support/v7/widget/GridLayoutManager.html#GridLayoutManager(android.content.Context,_int)) which offers a `spanCount parameter to set the number of columns.

    public GridLayoutManager (Context context, int spanCount)

The problem is that depending on the width of the screen, we can need showing more columns or less.

The [GridView](http://developer.android.com/guide/topics/ui/layout/gridview.html) view implements an attribute `android:numColumns = "auto_fit"` to this end, unfortunately, it is not implemented in the `RecyclerView`, the idea is to reuse that attribute to achieve the same objective.

[Chiu-Ki Chan](https://www.blogger.com/profile/01970007638489793840), already met this problem and her solution was posted in her [blog](http://blog.sqisland.com/2014/12/recyclerview-autofit-grid.html). Broadly speaking it consists of setting the parameter `spanCount` depending on the width of the screen.

Result:

![](http://androcode.es/wp-content/uploads/2015/03/autogrid.png)

<br>
## Multiples resources

The role of resources in the android framework is undisputed, the experience that a user can have on a nexus 5 can be very different to that sits in a 10" nexus, the (`MoviesDetailActivity`) has the elements differently depending on the screen that is displayed.

![](http://androcode.es/wp-content/uploads/2015/03/detailFamily-e1426180053215.png)

<br>


The optimum is to get this result with the least number of modifications both in the actual activity, `MovieDetailActivity` as the layout `activity_detail.xml`

For this see the application resources:

![](http://androcode.es/wp-content/uploads/2015/03/resources-e1426180398592.png)
<br>
We could divide notable distinctions between devices in the following points:

- Devices with less of **600dp** of screen width will use the folders **without** the modifier **-w600dp**, here you could categorize the vast majority of phones, for example: nexus 5, nexus 4, etc.

- Devices with more of **600dp** of screen width will be divided into three types: **-w600dp**, **-w600dp-land** and **-w600dp-port**.

In this way, we can go configuring our layouts and dimensions in different folders to change the layout according to the screen in which we find ourselves.

Also the issue that the `VectorDrawable` are not available until version had been mentioned. Is not availabe until the **v21** of the _framework_, so **-v21** modifier is for that purpose.

For example, the `ImageView` showing `VectorDrawable` star, is different if it is a device with _LLolipop_ or not, (`<include>`).

*activity_detail.xml* _(all versions)_


```xml
<FrameLayout>
    <!-- awesome hidden code -->

    <include
        layout="@layout/imageview_star"
    />

</FrameLayout>
```

*imageview_star.xml* (layout-v21)

```xml
<?xml version="1.0" encoding="utf-8"?>
<ImageView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_detail_confirmation_image"
    android:layout_width="300dp"
    android:layout_height="300dp"
    android:layout_gravity="center"
    android:src="@drawable/avd_star"
    />
```


*imageview_star.xml* (layout)


```xml
<?xml version="1.0" encoding="utf-8"?>
<ImageView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_detail_confirmation_image"
    android:layout_width="150dp"
    android:layout_height="150dp"
    android:layout_gravity="center"
    android:src="@drawable/star"
    />
``` 

I have implemented this solution, but the modularity of resources offers many possible solutions, for example, the _width_ and _height_ could point to a dimension in the folders _*values*/dimen.xml_ and _ *values-v21*/dimen.xml_, with `150dp` and `300dp`; The `drawable` could be the vector called _drawable-v21/*star. xml*_ and in the folder _drawable/*start.png*_ file with the image in _.png_.

<br>
## Parallax landscape view


If you check the Google Books app, you will see that you can seea small view below the `Toolbar`, when you scroll the list of books that _view_ moves at a different speed than the `Toolbar` creating an effect of `Parallax`.

![](http://androcode.es/wp-content/uploads/2015/03/books.png)

There are some great series called [How to hide/show Toolbar when list is srolling](http://mzgreen.github.io/2015/02/15/How-to-hideshow-Toolbar-when-list-is-scroling%28part1%29/) by [Michal Z.](http://mzgreen.github.io/) that treated this topic in deeper.

For layouts '-w600dp-land` and higher:

```xml
    <View
        android:id="@+id/activity_movies_background_view"
        android:layout_width="match_parent"
        android:layout_height="@dimen/
        activity_movies_background_view_height"
        android:background="@color/theme_primary"
        />`
```

This view is injected by [ButterKnife](http://jakewharton.github.io/butterknife/), being in a nexus 5, the layout you would come from 'activity_movies.xml' in the folder (layouts), where that view does not exist, so we must mark the view with the annotation: '@Optional'.

_MoviesActivity.java_

``` java
@Optional
@InjectView(R.id.activity_movies_background_view) 
View mTabletBackground;
```

_MoviesActivity.java_

```java

private RecyclerView.OnScrollListener recyclerScrollListener = 
    new RecyclerView.OnScrollListener() {

    @Override
    public void onScrolled(RecyclerView recyclerView, 
        int dx, int dy) {
        
        // awesome hidden code here

        if (mTabletBackground != null) {

            mBackgroundTranslation = mTabletBackground
                .getY() - (dy / 2);

            mTabletBackground.setTranslationY(mBackgroundTranslation);
        }
    }
```
