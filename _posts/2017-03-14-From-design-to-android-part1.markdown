---
layout: post
permalink: from-design-to-android-part1
title: From design to android, part 1
---

Thanks to amazing design platforms like [Dribbble][link2] or [MaterialUp][link3], we, as developers have access to a huge catalog of well-made design concepts, interfaces, and proposals in clicks. Nevertheless, sometimes some details are almost impossible to implement and, at times, some UX aspects are not taken into account.

For that reason I thought that it would be nice to create a project about writing a series of posts picking some [Dribbble][link2] or [MaterialUp][link3] shots and implementing them on android, explaining some implementation details and UI/UX android tips which I think are important.



<br/>
## The concept

This is the concept that I've chosen for the first part, simple and complex enough to stop on some interesting topics like [ConstraintLayout][link4] & [chains][link5], [DataBinding][link6], the importance of a performant UI hierarchies and [scenes][link7].

- The author of this concept [Johnyvino][link8], and has been published on [MaterialUp][link9]
- The implementation can be found at [this repository](https://github.com/saulmm/from_design_to_android_part1) on GitHub.


<br>
![](http://saulmm.github.io/resources/codeUI1/concept.gif)

Let's get started!

<br/>
### Bottom sheets from the support library

In the shot, a [bottom sheet][link12] is used for guiding the user through a buying process. In android you can find a couple of third-party libraries for implementing this kind of views, as the [Umano's AndroidSlidingUpPanel library][link10].

Relatively recently, bottoms sheets were included into the [design support library][link11] so I decided to using them to learn how they work.

<br/>
![](http://saulmm.github.io/resources/codeUI1/bottom_sheets.gif)
<br/>

Bottom sheets can be used in two ways, persistently supplementing the main view (using a [BottomSheetBehavior][link13] attached to a viewgroup inside a [CoordinatorLayout][link14]) or, if the information is shown in a modal way, we can use a [BottomSheetDialogFragment][link15].

For this example I've opted for the [BottomSheetDialogFragment][link15] since the information –being less important that the main view and needing focus– is shown eventually in a modal way. In terms of code, this new element is used similar to a [DialogFragment][link16].

`HomeActivity.java`

```java
OrderDialogFragment.newInstance(
  fakeProducts.get(position))
    .show(getSupportFragmentManager(),null);
```

`OrderDialogFragment.java`

```java
  @Override
public View onCreateView(LayoutInflater inflater,
    ViewGroup container,Bundle savedInstanceState) {

    super.onCreateView(inflater,
        container, savedInstanceState);

    binding = FragmentOrderFormBinding.inflate(
        inflater, container, false);

    return binding.getRoot();
}
```

<br/>
## ConstraintLayout

This powerful layout presented at Google I/O '16 has recently came to their stable version (1.0.2 at the time of the writing).

Among other benefits, it allows to build complex and responsive layouts in a flat hierarchy of views.

<br/>
![](http://saulmm.github.io/resources/codeUI1/constraint_sample.png)
<br/>

A common advice in android is to **avoid deep hierarchies of views** inside our layouts, since damages the performance and the time that our UI will take to be drawn in the screen. That's what we got for free when we use the [ConstraintLayout][link4].

<br/>
![](http://saulmm.github.io/resources/codeUI1/constraint_hierarchy.png)
<br/>

Basically, [ConstraintLayout][link4] works similar to [RelativeLayout][link17], this is defining relations between views and the screen. However, as well as being more performant, [ConstraintLayout][link4] behaves really nice with Android Studio.

Among the benefits there are more interesting mechanisms like **chains** between views (when different views are reciprocally constrained) or **elements like guidelines** (guides that can be used for constraining views from a percentage or fixed distance).

<br/>
### Usage of chains

Let's imagine that we have two views, **A** and **B**, both constrained to the screen edges right/left respectively. If we constrain **A** for being right of **B**, and **B** to be left **A** these views can be disposed in a special way, in the constraint layout realm this is known as a **chain**.

<br/>
![](http://saulmm.github.io/resources/codeUI1/chain1.png)
<br/>

With the Android Studio layout editor is really easy to create and handle chains with the actions inside  contextual menu of the layout editor. By default, Android Studio creates spread chains, where the views  are spread out.

<br/>
![](http://saulmm.github.io/resources/codeUI1/chain_creation.gif)
<br/>

### Chain types

<br/>
![](http://saulmm.github.io/resources/codeUI1/chains-styles.png)
<br/>

In this example only spread and packed chains are used, however, there are plenty of types to choose depending on your needs, lately Google has improved a lot their documentation about [ConstraintLayout][link19] and [chains][link18], so there is no excuse to start using them!

<br/>
![](http://saulmm.github.io/resources/codeUI1/different_chains.png)
<br/>

## Translating selected views


This could look simple, every time the user taps on a product parameter, the selected view is translated left to the bottom left labels.

<br/>
![](http://saulmm.github.io/resources/codeUI1/translating_views.gif)

For that, a new view is added to the parent layout where the user just tapped.

<br/>
`OrderDialogFragment.java`

```java
private void transitionSelectedView(View v) {
    final View selectionView = createSelectionView(v);

    binding.mainContainer.addView(selectionView);

    startCloneAnimation(selectionView, getTargetView(v));
}
```

And then, the view is translated, this is done making use of the method  [beginDelayedTransition][link20] from [TransitionManager][link20]. That method, detects if there were changes in the layout and if so, performs the animation with the selected transition.

<br/>
`OrderDialogFragment.java`

```java
private void startCloneAnimation(View clonedView, View targetView) {
    clonedView.post(() -> {
        TransitionManager.beginDelayedTransition(
            (ViewGroup) binding.getRoot(), selectedViewTransition);

        // Fires the transition
        clonedView.setLayoutParams(SelectedParamsFactory
            .endParams(clonedView, targetView));
    });
}
```

<br/>
## ViewSwitcher

The [ViewSwitcher][link24], a widget maybe not very popular from the Android SDK which allows transitioning two views with _in_ and _out_ animations. For this case fits perfectly between the layout step changes in the purchase process.

<br/>
`fragment_order_form.xml`

```xml
<ViewSwitcher
    android:id="@+id/switcher"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:inAnimation="@anim/slide_in_right"
    android:outAnimation="@anim/slide_out_left"
    >

    <include
        android:id="@+id/layout_step1"
        layout="@layout/layout_form_order_step1"
        />

    <include
        android:id="@+id/layout_step2"
        layout="@layout/layout_form_order_step2"
        />
</ViewSwitcher>
```

<br/>
![](http://saulmm.github.io/resources/codeUI1/switcher.gif)
<br/>

`OrderDialogFragment.java`

```java
private void showDeliveryForm() {
    binding.switcher.setDisplayedChild(1);
    initOrderStepTwoView(binding.layoutStep2);
}
```

<br/>
## Databinding

For the implementation of this concept I've used [Databinding][link21], for me, is confortable containing the whole hierarchy of the layout in a single object, however, there are other fancy Databading mechanisms that worth mentioning.

<br/>
### Saving click listeners

Since in these layouts are plenty of listeners to handle I've decided to send with [Databinding][link21] a listener object to the binding, in the xml, each view will specify the method that is interested in and the event will be properly redirected to our object.

<br/>
`layout_form_order_step1.xml`

```xml
<data>
    <variable
        name="listener"
        type="com.saulmm.cui.OrderDialogFragment.Step1Listener"
        />
</data>

<CircleImageView
    android:id="@+id/img_color_blue"
    style="@style/Widget.Color"
    android:onClick="@{listener::onColorSelected}"
    />

<CircleImageView
    android:id="@+id/img_color_blue"
    style="@style/Widget.Color"
    android:onClick="@{listener::onColorSelected}"
    />

```

<br/>
`OrderDialogFragment.java`

```java
layoutStep1Binding.setListener(new Step1Listener() {
    @Override
    public void onSizeSelected(View v) {
        // ...
    }

    @Override
    public void onColorSelected(View v) {
        // ...
    }
})
```

<br/>
## Saving views with Spannables + databinding + ConstraintLayout

Let's think during a minute on how we'd implement this part of the concept

<br/>
![](http://saulmm.github.io/resources/codeUI1/date_layout.png)
<br/>

One solution could be set a vertical `LinearLayout` per item, wrapped into another horizontal `LinearLayout` which would include three items wrapped in one more `LinearLayout` which would contain the two labels and the two item containers.

<br/>
 _Don't do this at home_

![](http://saulmm.github.io/resources/codeUI1/bad_hierarchy.png)
<br/>

This doesn't looks too good, right? We talked before about **the importance of having a flat hierarchy**, so [ConstrainLayout][link4] as the container has to be the way to go.

A `LinearLayout` for each item is expensive, we could realize that we are showing just text in different sizes plus a border so maybe, in some way, we could make it work just one `TextView` per item.

<br/>
![](http://saulmm.github.io/resources/codeUI1/good_hierarchy.png)
<br/>

## Spannables

[Spannables][link22] could do the work of showing the text in different sizes, and with a drawable, the border will be properly displayed. In that way we'll save 3 views per item, far better from the first approach.

The problem using [Spannables][link22] is that depending if we are on a time or a date item the amount of increased sized letters will change.

With [Databinding][link21] and their amazing [BindingAdapters][link22] mechanisms, we could create an attribute to set the increased number of letters.

<br/>
`layout_form_order_step2.xml`

```xml
<TextView
    android:id="@+id/txt_time1"
    style="@style/Widget.DateTime"
    app:spanOffset="@{3}"
    />

<TextView
    android:id="@+id/txt_date1"
    style="@style/Widget.DateTime"
    app:spanOffset="@{2}"/>
```

<br>
`OrderDialogFragment.java`

```java
@BindingAdapter("app:spanOffset")
public static void setItemSpan(View v, int spanOffset) {
    final String itemText = ((TextView) v).getText().toString();
    final SpannableString sString = new SpannableString(itemText);

    sString.setSpan(new RelativeSizeSpan(1.75f), itemText.
        length() - spanOffset, itemText.length(),
        Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);

    ((TextView) v).setText(sString);
}
```

<br/>
## Scenes

Maybe the substantial point when implementing this concept is how to approach the transition between the book button and the confirmation view.

<br/>
![](http://saulmm.github.io/resources/codeUI1/scene_transition.gif)
<br/>

Is not so hard if we make use of smoke and mirrors. If we **stop seeing the button** as a button and looking  it as a container, we could realize that we can reach the animation with scenes.

Being the first scene the whole form and the second one the confirmation view, with the button as a shared element. This shared element is a view with shares the same view across scenes, the framework will care of properly animating the view given a transition.

Once we have the idea the logic of changing two scenes is straightforward.



<br/>
`OrderDialogFragment.java`

```java
private void changeToConfirmScene() {
    final LayoutOrderConfirmationBinding confBinding =
        prepareConfirmationBinding();

    final Scene scene = new Scene(binding.content,
        ((ViewGroup) confBinding.getRoot()));

    scene.setEnterAction(onEnterConfirmScene(confBinding));

    final Transition transition = TransitionInflater
        .from(getContext()).inflateTransition(
              R.transition.transition_confirmation_view);

    TransitionManager.go(scene, transition);
}
```
<br/>
## Result

<br/>
![](http://saulmm.github.io/resources/codeUI1/final_result.gif)
<br/>

<br/>
## References

- [ConstraintLayout Documentation](https://developer.android.com/training/constraint-layout/index.html) - Android developers
- [Mastering android drawables](https://speakerdeck.com/cyrilmottier/mastering-android-drawables) - Cyril Mottier
- [Efficient android layouts](https://speakerdeck.com/dlew/efficient-android-layouts-goto-conference) - Dan Lew
- [Plaid](https://github.com/nickbutcher/plaid) - Nick Butcher


[link1]:https://github.com/umano/AndroidSlidingUpPanel "Umano"
[link2]:https://dribbble.com/ "Dribbble"
[link3]:https://material.uplabs.com/ "MaterialUp"
[link4]:https://developer.android.com/training/constraint-layout/index.html "ConstraintLayout"
[link5]:https://developer.android.com/training/constraint-layout/index.html#constrain-chain "Chains"
[link6]:https://developer.android.com/topic/libraries/data-binding/index.html "DataBinding"
[link7]:https://developer.android.com/training/transitions/scenes.html "Scenes"
[link8]:https://material.uplabs.com/johnyvino "Johnyvino"
[link9]:https://material.uplabs.com/posts/preferred-date-and-time "Prefered date and time"
[link10]:https://github.com/umano/AndroidSlidingUpPanel "Umano"
[link11]:https://developer.android.com/training/material/design-library.html "Design library"
[link12]:https://material.io/guidelines/components/bottom-sheets.html "Bottom Sheets"
[link13]:https://developer.android.com/reference/android/support/design/widget/BottomSheetBehavior.html "Bottom sheet behavior"
[link14]:https://developer.android.com/reference/android/support/design/widget/CoordinatorLayout.html "CoordinatorLayout"
[link15]:https://developer.android.com/reference/android/support/design/widget/BottomSheetDialogFragment.html "BottomSheetDialogFragment"
[link16]:https://developer.android.com/reference/android/app/DialogFragment.html "DialogFragment"
[link17]:https://developer.android.com/reference/android/widget/RelativeLayout.html "RelativeLayout"
[link18]:https://developer.android.com/reference/android/support/constraint/ConstraintLayout.html#Chains "Chains2"
[link19]:https://developer.android.com/reference/android/support/constraint/ConstraintLayout.html "ConstraintLayout2"
[link20]:https://goo.gl/QwCNwA "beginDelayedTransition"
[link21]:https://developer.android.com/topic/libraries/data-binding/index.html "Databinding"
[link22]:https://developer.android.com/reference/android/text/Spannable.html "Spannables"
[link23]:https://developer.android.com/reference/android/databinding/BindingAdapter.html "BindingAdapter"
[link24]:https://developer.android.com/reference/android/widget/ViewSwitcher.html "ViewSwitcher"
