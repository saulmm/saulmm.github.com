---
layout: post
permalink: a-useful-stack-on-android-2-user-interface
---

This is the second part of the series: _'A useful stack on android'_, in the [first part](http://saulmm.github.io/2015/02/02/A%20useful%20stack%20on%20android%20%231,%20architecture/), I reviewed the general architecture of the proyect, this article aims to focus into the user interface and the global design of the application.

I would not like to talk about how to _materialize_ an android application with [Material Design](http://www.google.com/design/spec/material-design/introduction.html) you can find a nice keynote by David Gonzalez [here](https://plus.google.com/+davidgonzalezmalmstein/posts/MHRQC1M2HGv).

Looking the design structure, there is only two activities, `MoviesActivity` with a [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html) containing all the films & a `MovieDetailActivity`, that shows some details of the selected film.

This project is available on [GitHub](https://github.com/saulmm/Material-Movies)

<br>
## Libraries

<br>
`app/build.gradle`

```javascript
    // Google libraries
    compile 'com.android.support:appcompat-v7:21.0.3'
    compile 'com.android.support:recyclerview-v7:21.0.3'
    compile 'com.android.support:palette-v7:21.0.0'

    // Square libraries
    compile 'com.squareup.picasso:picasso:2.4.0'
    compile 'com.jakewharton:butterknife:6.0.0'
```

<br>
### AppCompat

What to say about the new AppCompat [library](http://android-developers.blogspot.com.es/2014/10/appcompat-v21-material-design-for-pre.html) from Google, In this library it is included a new component, the [Toolbar](http://developer.android.com/reference/android/widget/Toolbar.html).

The `Toolbar` element is a generalization of the old: [_Action Bar_](http://developer.android.com/reference/android/app/ActionBar.html). 

This new _widget_ is a [ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html), so we can group views within it, in my case I have included a custom `TextView` with a particular font. In addition, taking advantage of the flexibility, when the user _scrolls down_ the toolbar is hidden, when the user _scrolls up_ the toolbar gets shown.

![](http://androcode.es/wp-content/uploads/2015/02/bar.gif)
<br>
`activity_main.xml`

```xml
<android.widget.Toolbar
    android:id="@+id/activity_main_toolbar"
    android:layout_height="wrap_content"
    android:layout_width="match_parent"
    android:minHeight="?attr/actionBarSize"
    android:background="@color/theme_primary"
    android:elevation="10dp"
    >

    <com.hackvg.android.views.custom_views.LobsterTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/app_name"
        android:textSize="22sp"
        android:textColor="#FFF"
        />

</android.widget.Toolbar>
```

<br>
`MoviesActivity.java`

```java
private RecyclerView.OnScrollListener recyclerScrollListener = 
    new RecyclerView.OnScrollListener() {

    public boolean flag;

    @Override
    public void onScrolled(RecyclerView recyclerView, 
        int dx, int dy) {

        super.onScrolled(recyclerView, dx, dy);

        // Is scrolling up
        if (dy > 10) {

            if (!flag) {

                showToolbar();
                flag = true;
            }

        // Is scrolling down
        } else if (dy < -10) {

            if (flag) {

                hideToolbar();
                flag = false;
            }
        }
    }
};

private void showToolbar() {

    toolbar.startAnimation(AnimationUtils.loadAnimation(this,
        R.anim.translate_up_off));
}

private void hideToolbar() {

    toolbar.startAnimation(AnimationUtils.loadAnimation(this,
        R.anim.translate_up_on));
}
```

<br>
`translate_up_off.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>

<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:interpolator/fast_out_linear_in"
    android:fillAfter="true">

    <translate
        android:duration="@integer/anim_trans_duration_millis"
        android:startOffset="0"
        android:fromXDelta="0"
        android:fromYDelta="0"
        android:toXDelta="0"
        android:toYDelta="-100%"
        />
</set>
```

<br>
### ButterKnife

[ButterKnife](http://jakewharton.github.io/butterknife/) by [Jake Wharton](https://github.com/JakeWharton), is a library to perform view injections.

This avoids having to write repetitive sentences like: `findViewById`, `setOnClickListener(new OnClick...)` etc... In this way, the code is more readable in adition to write much less.

<br>
`MovieDetailActivity.java`

```java
    @InjectViews({
        R.id.activity_movie_detail_title,
        R.id.activity_movie_detail_content,
        R.id.activity_detail_homepage_value,
        R.id.activity_detail_company_value,
        R.id.activity_detail_tagline_value,
        R.id.activity_movie_detail_confirmation_text,
    }) List<TextView> movieInfoTextViews;

    @InjectViews({
        R.id.activity_detail_header_tagline,
        R.id.activity_detail_movie_header_description
    }) List<TextView> headers;
    
    @InjectView(R.id.activity_detail_book_info) 
    View overviewContainer;
    @InjectView(R.id.activity_movie_detail_fab)
    ImageView fabButton;
    @InjectView(R.id.activity_movie_detail_cover_wtf)
    ImageView coverImageView;
    @InjectView(R.id.activity_movide_detail_confirmation_image)
    ImageView confirmationView;
    @InjectView(R.id.activity_movie_detai_confirmation_container)
    FrameLayout confirmationContainer;

    @InjectView(R.id.activity_movie_detail_scroll)
    ObservableScrollView observableScrollView;
```

An interesnting fact of this library is that the annotation: `@InjectViews`, wich allows to inject multiple views in a list, so you can use interfaces as `Setters` or `Actions` to apply a property in all views inside the list.

<br>
`GUIUtils.java`

```java
public static final ButterKnife.Setter<TextView, Integer> setter = new ButterKnife.Setter<TextView, Integer>() {

    @Override
    public void set(TextView view, Integer value, int index) {
        view.setTextColor(value);
    }
};
```

In my case all `TextViews` that display information about the film are set with a certain text color.

`MoviesActivity.java`

```java
ButterKnife.apply(movieInfoTextViews, GUIUtils.setter, 
    lightSwatch.getTitleTextColor());
```

With `ButterKnife` also you can handle some view events:

```java
@OnClick(R.id.activity_movie_detail_fab)
public void onClick() {
    showConfirmationView();
}
```

<br>
## Palette

With Android L Google has introduced a new library called [_Palette_](https://developer.android.com/reference/android/support/v7/graphics/Palette.html), it promises to extract the predominant colors in an image.

<br>
![MovieDetailActivity](http://androcode.es/wp-content/uploads/2015/02/palette.png)
<br>
These colors are grouped in a container called [_Swatch_](https://developer.android.com/reference/android/support/v7/graphics/Palette.Swatch.html), wich contains among other properties a background color and a color for the text readable in conjunction with the background color.

With _Palette_ you can obtain the following sets of colors:

- `MutedSwatch`
- `VibrantSwatch`
- `DarkVibrantSwatch`
- `DarkMutedSwatch`
- `LightMutedSwatch`
- `LightVibrantSwatch`

For this application I have only used: `VibrantSwatch`, `DarkVibrantSwatch` and `LightVibrantSwatch`.

![](http://androcode.es/wp-content/uploads/2015/02/palette2.png)
<br>

Keep in mind that you can not always to obtain certain colors in an image, so it is recommended to check that _Palette_ not return __null__ sets.

Another aspect to consider is that the task of determining colors is a complex task, so _Palette_ provides an asynchronous way to generate the colors.

<br>
`MoviesActivity.java`

```java
Palette.generateAsync(bookCoverBitmap, this);
```

```java
public class MovieDetailActivity extends Activity implements 
    MVPDetailView, Palette.PaletteAsyncListener {
    ...

        @Override
    public void onGenerated(Palette palette) {

        if (palette != null) {

            Palette.Swatch vibrantSwatch = palette
                .getVibrantSwatch();

            Palette.Swatch darkVibrantSwatch = palette
                .getDarkVibrantSwatch();

            Palette.Swatch lightSwatch = palette
                .getLightVibrantSwatch();

            if (lightSwatch != null) {

                // awesome palette code
            }
        }
    }
}
```

I found a very some interesting concepts in the _Dialer_ app at Lollipop. In the contact detail view, the icons are tinted depending the tint color that is applied to the contact picture:

![](http://androcode.es/wp-content/uploads/2015/02/dialer_colors1.png)

This effect can be achieved by dynamically applying a [ColorFilter](http://developer.android.com/reference/android/graphics/ColorFilter.html) to the _CompoundDrawable_ of the _TextView_

<br>
`GUIUtils.java`

```java
  public static void tintAndSetCompoundDrawable (Context context, 
    @DrawableRes int drawableRes, int color, TextView textview) {

        Resources res = context.getResources();
        int padding = (int) res.getDimension(
            R.dimen.activity_horizontal_margin);

        Drawable drawable = res.getDrawable(drawableRes);
        drawable.setColorFilter(color, PorterDuff.Mode.MULTIPLY);

        textview.setCompoundDrawablesRelativeWithIntrinsicBounds(
            drawable, null, null, null);

        textview.setCompoundDrawablePadding(padding);
    }
```

Result:

![](http://androcode.es/wp-content/uploads/2015/02/compund.png)
<br>
### Transitions

The transition between the activity: `MoviesActivity`, and the `MovieDetailActivity`, makes use of a shared element that is the cover image of the selected film.

In the adapter of the `RecyclerView` is specified the `transitionName` that will be necessary to perform the transition.

```java
    @Override
    public void onBindViewHolder(MovieViewHolder holder, 
        int position) {

        TvMovie selectedMovie = movieList.get(position);

        holder.titleTextView.setText(selectedMovie.getTitle());
        holder.coverImageView.setTransitionName("cover" + position);

        String posterURL = Constants.POSTER_PREFIX 
            + selectedMovie.getPoster_path();

        Picasso.with(context)
            .load(posterURL)
            .into(holder.coverImageView);
    }
```

Before change to the activity detail, is specified in the intent the element that will be shared with the `ActivityOptions`.

```java
    @Override
    public void onClick(View v, int position) {

        Intent i = new Intent (MoviesActivity.this, 
            MovieDetailActivity.class);

        String movieID = moviesAdapter.getMovieList()
            .get(position).getId();

        i.putExtra("movie_id", movieID);
        i.putExtra("movie_position", position);

        ImageView coverImage = (ImageView) v.findViewById(
            R.id.item_movie_cover);

        photoCache.put(0, coverImage
            .getDrawingCache());

        // Setup the transition to the detail activity
        ActivityOptions options = ActivityOptions
            .makeSceneTransitionAnimation(this, 
            new Pair<View, String>(v, "cover" + position));

        startActivity(i, options.toBundle());
    }
```

Finally in the detail activity is specified which view is shared collecting the _metadata_ from the incoming intent.

```java
    @Override
    public void onCreate(Bundle savedInstanceState) {

        ...

        int moviePosition = getIntent()
            .getIntExtra("movie_position", 0);
        
        coverImageView.setTransitionName(
            "cover" + moviePosition);

        ...
```

These could be the requirements of any application with a list and a detail view, but what if there is an intermediate state between the detail, and with the returning to the list?

When a user presses the `Floating Action Button` to set a movie as favorite, a view is  briefly presented to inform the user that the save has been successful.

After that I am not interested in making use of the transition: `sharedElementReturnTransition` for return to the main activity, what interests me is to show an animation that changes the user experience.

Is not the same to mark a film as favorite that return without do anything with the film, so the design has to behave differently.
<br>

![](http://androcode.es/wp-content/uploads/2015/02/transitions.gif)

<br>

When the confirmation view is shown, the return transition is overwritten, the shared element effect (the film cover) will not be animated and the return effect will be to pull down the activity: `getWindow().setReturnTransition(new Slide());`

![](http://androcode.es/wp-content/uploads/2015/02/transitions_group.png)

### VectorDrawable

A very interesting feature introduced with lollipop are the [_VectorDrawables_](https://developer.android.com/reference/android/graphics/drawable/VectorDrawable.html).

With the new drawables a new world opens to the vector graphics, image scalling etc... 

_Lollipop_ also includes powerful tools to deal with these new graphics.

The `VectorDrawable` makes use of part of the` SVG` specification, for example, this is a star in `SVG` format:

<br>
![](http://androcode.es/wp-content/uploads/2015/02/star.png)
<br>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1"  xmlns="http://www.w3.org/2000/svg" 
    width="300px" 
    height="300px" >

    <g id="star_group">
        <path fill="#000000" d="M 200.30535,69.729172
        C 205.21044,69.729172 236.50709,141.52218 240.4754,144.40532
        C 244.4437,147.28846 322.39411,154.86809 323.90987,159.53312
        C 325.42562,164.19814 266.81761,216.14828 265.30186,220.81331
        C 263.7861,225.47833 280.66544,301.9558 276.69714,304.83894
        C 272.72883,307.72209 205.21044,268.03603 200.30534,268.03603
        C 195.40025,268.03603 127.88185,307.72208 123.91355,304.83894
        C 119.94524,301.9558 136.82459,225.47832 135.30883,220.8133
        C 133.79307,216.14828 75.185066,164.19813 76.700824,159.53311
        C 78.216581,154.86809 156.16699,147.28846 160.13529,144.40532
        C 164.1036,141.52218 195.40025,69.729172 200.30535,69.729172 z"/>
    </g>
</svg>
```

An this, its implementation with a `VectorDrawable`

`vd_star.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:viewportWidth="400"
    android:viewportHeight="400"
    android:width="300px"
    android:height="300px">

    <group android:name="star_group"
        android:pivotX="200"
        android:pivotY="200"
        android:scaleX="0.0"
        android:scaleY="0.0">

        <path
            android:name="star"
            android:fillColor="#FFFFFF"
            android:pathData="@string/star_data"/>
    </group>
</vector>
```

`strings.xml`

```xml
<string name="star_data">
    M 200.30535,69.729172
    C 205.21044,69.729172 236.50709,141.52218 240.4754,144.40532
    C 244.4437,147.28846 322.39411,154.86809 323.90987,159.53312
    C 325.42562,164.19814 266.81761,216.14828 265.30186,220.81331
    C 263.7861,225.47833 280.66544,301.9558 276.69714,304.83894
    C 272.72883,307.72209 205.21044,268.03603 200.30534,268.03603
    C 195.40025,268.03603 127.88185,307.72208 123.91355,304.83894
    C 119.94524,301.9558 136.82459,225.47832 135.30883,220.8133
    C 133.79307,216.14828 75.185066,164.19813 76.700824,159.53311
    C 78.216581,154.86809 156.16699,147.28846 160.13529,144.40532
    C 164.1036,141.52218 195.40025,69.729172 200.30535,69.729172 z
</string>
```

There is little difference, there are groups, paths etc..., `android:viewport{Width|Height}` specifies the size of the canvas, while `android:width` & `android:height` specifies the image size.

The `<animated-vector>` allows to animate groups of `<paths>`, translations, rotations and among other animations, morphing.

In this case for example, a star is shown with a scaling animation and when it ends, an animation of rotation is shown. At the same time the shape  of the star is changing to the stick candy shape, and then changes again to its original shape, a star.

It is noteworthy that to make it possible to show a morphing animation , the data has to contain the __same__ `SVG` commands if not, an exception will be triggered.

`avd_star.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/vd_star">

    <target
        android:name="star_group"
        android:animation="@anim/appear_rotate" />

    <target
        android:name="star"
        android:animation="@anim/star_morph" />

</animated-vector>
```
The `<animated-vector>` is associated to the _drawable_ `vd_star.xml`, the targets, are the elements that will be animated:

- The first group is focused in the group: `start_group` defined on the vector: `star.xml`, this will run an animation of scaling and rotation.

`appear_rotate.xml`

```xml
<set
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="sequentially"
    android:interpolator="@android:anim/decelerate_interpolator"
    >

    <set
        android:ordering="together"
        >

        <objectAnimator
            android:duration="300"
            android:propertyName="scaleX"
            android:valueFrom="0.0"
            android:valueTo="1.0"/>

        <objectAnimator
            android:duration="300"
            android:propertyName="scaleY"
            android:valueFrom="0.0"
            android:valueTo="1.0"/>
    </set>

    <objectAnimator
        android:propertyName="rotation"
        android:duration="500"
        android:valueFrom="0"
        android:valueTo="360"
        android:valueType="floatType"/>
</set>
```
- For the second target a morph animation is applied, this is another `<objectAnimator>` to change the `SVG` data to another `SVG` data.

I would like emphasize that the mutation will be succesfull if the `SVG` commands are the same in the new `SVG` data but with different values.

In this `<set>` is changed the star shape to a stick candy, after the shape returns to be a star.

`star_morph.xml`

```xml
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="sequentially"
    android:fillAfter="true">

    <objectAnimator
        android:duration="500"
        android:propertyName="pathData"
        android:valueFrom="@string/star_data"
        android:valueTo="@string/star_lollipop"
        android:valueType="pathType"
        android:interpolator="@android:anim/accelerate_interpolator"/>

    <objectAnimator
        android:duration="500"
        android:propertyName="pathData"
        android:valueFrom="@string/star_lollipop"
        android:valueTo="@string/star_data"
        android:valueType="pathType"
        android:interpolator="@android:anim/accelerate_interpolator"/>
</set>
```

Resutl:

<br>
![](http://androcode.es/wp-content/uploads/2015/02/animated.gif)
<br>
## Sticky headers

Another aspect that caught my attention in the implementation of Google Dialer is that when you `scroll` in a contact information, the image becomes smaller until a point. In that point the image becomes fixed.

<br>
![](http://androcode.es/wp-content/uploads/2015/02/dialer.gif)
<br>

Trying to replicate this effect I have found a post by [Roman Nurik](https://code.google.com/p/romannurik-code/source/browse/misc/scrolltricks/src/com/example/android/scrolltricks/StickyFragment.java), where the property: `View.setTranslationY(float translationY)` is used for this purpose in a `ScrollView` listener.

To achieve this effect, the `translationY` conjugated with the displaced value in the `ScrollView` allows to set the title fixed.

`MovieDetailActivity.xml`

```java

    @Override
    public void onScrollChanged(ScrollView scrollView, 
        int x, int y, int oldx, int oldy) {

        if (y > coverImageView.getHeight()) {

            movieInfoTextViews.get(TITLE).setTranslationY(
                y - coverImageView.getHeight());

            if (!isTranslucent) {
                GUIUtils.setTheStatusbarNotTranslucent(this);
                getWindow().setStatusBarColor(mBrightSwatch.getRgb());
                isTranslucent = true;
            }
        }

        if (y < coverImageView.getHeight() && isTranslucent) {

            GUIUtils.makeTheStatusbarTranslucent(this);
            isTranslucent = false;
        }
    }
```

** _There are still a few bugs in this section, for example, if you make a quickly scroll a gap is created between the cover image and the title_, _Pull request welcome!_
<br>
## Resources:

- [First look at AnimatedVectorDrawable](http://blog.sqisland.com/2014/10/first-look-at-animated-vector-drawable.html) - Chiu-Ki Chan

- [VectorDrawables series] (https://blog.stylingandroid.com/vectordrawables-part-1/) - Styling android

- [appcompat v21: material design for pre-Lollipop devices!](https://chris.banes.me/2014/10/17/appcompat-v21/) - Chris Banes


