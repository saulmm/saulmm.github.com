---
layout: post
permalink: the-powerful-android-studio
title: The powerful Android Studio
---

[Android Studio][14] is the official tool for Android development these days. Being developed at the top of project [IntelliJ IDEA][8], takes into advantage (almost in its entirety) features of edition, debugging, analysis, refactor among many other categories for developing in an effective way.

In the latest version (2.2 at the time of the writing), [Android Studio][14] includes a lot of improvements like the new UI editor, [interaction][10] with the new _ConstraintLayout_ viewgroup and much more.

This article doesn't put the focus on covering these new features. In this one, I would like to give importance to the role that plays [IntelliJ IDEA][8] over [Android Studio][14] besides a few more tips that I use every day.

Because of my last talk given at the amazing event [Exfest'16](http://exfest.es/), the intention of this article is to serve as media support (there weren't slides) of the talk, and of course, a new excuse to write a new article :),. Let's get started!

#### Disclaimer

- All shorcuts shown in this article corresponds to the `Mac OS X 10.5+`

- The green box which suggests the shotcut used at the bottom of the gifs is a nice plugin called [Presentation Assistant](https://plugins.jetbrains.com/plugin/7345), perfect for presentations and pair programming :).

<br>
## Miscellaneous

### Avoid show the logcat automatically

It could be interesting to disable the expansion of the window _Android Monitor_ during every time when run our app (since the default behavior on a run configuration is to show it). 

To achieve this, in the _Run Configuration_ that we are using in our project just disable that tick at `Edit Configurations/Android Application/Miscellaneous.

![](http://saulmm.github.io/resources/studio/disable_logcat_on_run.gif)

<br>
### No tabs


As [Hadi Hadiri](https://twitter.com/hhariri) rightly says in his [article][1], using tabs could result in the following hassles: loss of context, a valuable space in the editor consumed and the fact that also the interaction with tabs is used to require using the trackpad or the mouse. 

If you believe that tabs don't offer any benefit to you,  disable them under `settings/editor/tabs` setting the _Placement_ preference to **_None_**.

![](http://saulmm.github.io/resources/studio/no_tabs.png)

[IntelliJ IDEA][6] offers many ways to move effectively through the code without need tabs.

<br>
## Navigation

One of the goals which the [JetBrains][7] team encourages to the users is that we have to use the mouse as less as possible. There are tons of actions and commands which allow working in a very effective way without leaving the hands from the keyboard gaining speed and accuracy.

### Find classes, files, and actions

[IntelliJ IDEA][8] and, as the result, [Android Studio][10] offers solutions finding files, classes, actions.

#### Search Everywhere - ⇧ + ⇧
It shows a dialog for search all types of elements: classes, files, actions, etc. It's recommended to avoid this action and use the specific ones since _search everywhere_ could be slower and expensive in term of your machine resources.

#### Search types - ⌘ + O
It allows finding enumerations, classes, interfaces, etc fastly.

#### Search files -  ⌘ + ⇧ + O
It allows finding every type of files, useful for XML files like layouts, resources, etc.

#### Search actions - ⇧ + ⌘ + o + A
It's possible to search and run actions from this feature, actions that live under menus, preferences, tool windows, etc, also suggests the shortcut of some actions, so, if you forget it you can fastly remind it searching for the action.

![](http://saulmm.github.io/resources/studio/look_for_stuff.gif)

**_Bonus:_**

- It's not necessary to write the full name of whatever we are looking for. If we are searching for a class called `CharacterDetailPresenter`,  we'll find it just typing `CharacterPresenter`.

- We can go to a specific line looking for classes (⌘ + O) writing a colon and the number of the line after the searched class name, for example: `CharacterDetailPresenter:50`.

- If we type `/` on the beginning of the text of the file that we are looking for, we'll find folders. For example, with `/land`, the dialog will suggest the folders related to landscape configurations.

- We can filter the folder which contains the searched file, for example, `values-es/strings.xml` or `values/strings.`

<br>
### Project window - ⌘ + 1
    
For browsing through the project files, a nice solution is to use the project window which can be shown or hidden with the shortcut **⌘ + 1**. 

![](http://saulmm.github.com/resources/studio/show_panel_hide.gif)

**_Bonus:_**

- With **⌘ + ⇧ + ⟵ / ⟶**, we can modify the size of the focused panel without using the mouse.

- We can perform searches writing the name of 
the file or folder that we are looking for right in the browser, it will highlight the matches and will restrict the positions selected with the arrows for these matches.

<br>
### Jump to navigation bar - ⌘ + ↑

The navigation bar is an interesting element to interact with. Using it,  we can navigate through the project files as we usually do with the project window. The shortcut **⌘ + ↑** displays the bar and puts the focus into it even if it's disabled.

![](http://saulmm.github.com/resources/studio/jump_to_navigation_bar.gif)

**_Bonus:_**

- We can perform some file operations inside the navigation bar context, like create new files **⌘ + N** or delete them **⌫**.

<br>
### Show and hide windows

There are different ways to manipulate windows: show and hide a single one,  jump to the last active window giving it focus, restore the focus back to the editor when we are using a panel, etc.

![](http://saulmm.github.io/resources/studio/panels_operations.gif)

#### Hide / Restore all windows - ⇧ + ⌘ + F12
As its name indicates, it allows to hide and restore all  visible windows.

#### Jump to Last Tool Window - F12
Restores the visibility of the last window used, also puts the focus into that tool.

#### Back to the editor
When we have the focus in a window, we can back to the editor without using the mouse with the key **⎋**.

<br>
### Recently files

These three actions can help us to save time when we are working with a group of files:

#### Recently Files - ⌘ + E
It shows files that have been opened recently.

#### Recently Changed Files - ⌘ + ⇧ + E
It shows files that have been opened and changed recently.

![](http://saulmm.github.io/resources/studio/recent_files_recent_edited_files.gif) 

<br>
### Structure of a file

A quick method to navigate through the different attributes and methods in a class is using the action _file scructure_, can be used with   shortcut ⌘ + F12, or finding the action with ⌘ + A and typing _file structure_.

This feature, as many other, allows searching on it, allowing find quickly what we are looking for and place the caret accordingly.

![](http://saulmm.github.io/resources/studio/look_for_methods.gif) 

Similarly, the window _structure_  (⌘ + 7), shows the same information but in a permanent way.

<br>
### Show implementations and Super Method

It's useful, sometimes, to show the current implementations of a superclass, interface or a method. Using ⌘ + B in the name of the class or method selected, allows, in a glance, check the current implementations navigate to them pressing the ENTER key.

Similarly, with the shortcut ⌘ + U in an implementation, we can navigate to the method or class which is being implemented.

![](http://saulmm.github.io/resources/studio/navigate_implementations_superclass.gif)

<br>
### Next Highlighted Error - F2

When Android Studio highlights some compilation errors, usually we use the mouse to scroll until their location and fix them.

With [IntelliJ IDEA][8], we can place the care right on the left of the erros using the feature _Next Highlighted Error_ (F2), avoiding leave your hands from your amazing keyboard. 

![](http://saulmm.github.io/resources/studio/next_error.gif)

First,  *F2* will iterate the caret over all the errors. Once they are solved, the cursor will be placed left of the warnings according to the current _inspection profile_.

<br>
### Las Edit Location -  ⌘ + ⇧ + ⌫ - _Last Edit Location_

When we are changing the code in classes with a huge number of lines, we tend to edit different sections of the class. Put some declarations, edit the body of a method, etc.

It could be useful after add some declarations place the caret back to the previous edition without using your mouse.  

The action _Last Edit Location_ (⌘ + ⇧ + ⌫), does exactly that. Moves the caret to the previous edition location without changing the code.

![](http://saulmm.github.io/resources/studio/last_edit_location.gif)

Even if the edition has been made in a different file, the editor will place the caret right in the file where the edition was made.

<br>
## Edition
### Replace instead append

When we are writing code, usually we use the completion dialog in order to add suggestions.

When we decide to add a suggestion pressing the ENTER key, the new sentence is added left of the old one, causing an error.

![](http://saulmm.github.io/resources/studio/editor_replace_instead_append.gif)

If instead ENTER we press TAB, and if desired method call has the same number of parameters, the new code will be automatically set with the old parameters.

<br>
### Complete Current Statement - ⌘ + ⇧ + ENTER - _Complete Current Statement_

Using a powerful IDE like Android Studio, there is no need to worry about some syntactic elements like braces or semicolons.

![](http://saulmm.github.io/resources/studio/editor_complete_current_statement.gif)

There is an action called _Complete Current Statement_ which automatically add the necessary elements in some contexts.


<br> 
### Add Selection To The Next Ocurrence - ⌘ + G

In some situations, it could be very effective use multiple carets in order to change multiple sentences at the same time.

We can achieve it, with the feature _Add Selection To The Next Ocurrence_ (⌘ + G), selecting the pattern where we want to add carets.

[Android Studio][14] and [IntelliJ IDEA][8] give us even some cool editing tools in this feature, like cut and paste fragments of code, selection tools, etc.

![](http://saulmm.github.io/resources/studio/multiple_cursors.gif)

A practical use could be, for instance, changes the binding of our views in an activity or fragment to replace them by [ButterKnife](http://jakewharton.github.io/butterknife/) annotations.

<br>
### Join Lines - ⌘ + ⇧ + J

When there is a  _string_ concatenated multiple times in different lines, it could be useful to join those lines into a single one, with the action _join lines_ we'll achieve a single sentence without having to worry about delete the concat operators.

![](http://saulmm.github.io/resources/studio/join_lines.gif)

<br>
### Check the parameters info - ⌘ + P

Using the completion for add a call to a method, [Android Studio][14] shows a popup with the required parameters, this popup hides after a few seconds.

Using the action _Parameter Info_ with ⌘ + P, we can check what parameters are needed for the desired method call every time we want.

![](http://saulmm.github.io/resources/studio/look_for_parameters.gif)

<br>
### Surround With - ⌘ + T

Sometimes, it could be interesting to wrap certain code for evaluating it with a condition, iterate through it or capture an exception.

For that purpose, [IntelliJ IDEA][8] and in consequence, [Android Studio][14], offers a tasty feature called _surround with_, operable with a dialog fired by the shortcut ⌘ + T.

![](http://saulmm.github.io/resources/studio/surround_with.gif)

With that dialog we can choose among different actions: conditions, loops, exceptions, etc, even _live templates_. 

**_Bonus:_**

If we fire that dialog in a XML context like a layout file, a few suggestions will be shown. With a bit of ingenuity and a _live template_ we could, for example, wrap a view or _viewgroup_ into another _viewgroup_, in a very easy way.

This is an example of a _live template_ used with the _surround with_ dialog to wrap views insider viewgroups.

![](http://saulmm.github.io/resources/studio/xml_live_template.png)

<br>
### Move sentence up/down - ⌘ + ⇧ + ↑/↓ 

In order to move code is not needed to cut and paste sentences manually. The action _Move Sentence Up/Down_ under the selection of a sentence of a group of ones, will be useful to move code in an effective way.

![](http://saulmm.github.io/resources/studio/move_up_down.gif)

**_Bonus_**

This action could result useful editing layout files in XML. If we select a view, we could move it into an existing viewgroup in the same file.

<br>
### Extend/Shrink selection - ⌥ + ↑/↓

[Android Studio][14], thanks to [IntelliJ IDEA][8] inherits a mechanism really interesting whe we select code.

![](http://saulmm.github.io/resources/studio/extend_shrink_selection.gif)

Using the shorcut ⌥ + ↑/↓  in a specific section of the code, will extend or collapse the selection until the next point regarding the nearest _scope_.

<br>
## Completition
### Live templates - ⌘ + J

The _live templates_ are a powerful mechanism to avoid to write boilerplate code. They can be customized with a lot of different options in `settings/editor/live templates`.

![](http://saulmm.github.io/resources/studio/live_templates.gif)

[Android Studio][14], by default, has a lot of live templates ready to use, both for contexts like _java_ and XML.

![](http://saulmm.github.io/resources/studio/available_live_templates.png)
![](http://saulmm.github.io/resources/studio/available_live_templates_2.png)

With the shortcut ⌘ + J we can show a dialog with the available _live templates_ for the context where we are.

<br>
## Debugging 
### Attach _debugger_ to Android process

It could be interesting avoid to run a _debug_ session from [Android Studio][14], (⌃ D), because we have tons of breakpoints configured,  we want to force a state before init the debug session, etc.

![](http://saulmm.github.io/resources/studio/attach_debugger.gif)

For that, [Android Studio][14] includes an action called _Attach Debugger to Android Process_.

<br>
### Conditional breakpoints

In code which is called multiple times, it could be annoying if our breakpoint is called every time which is fire, and maybe, our purpose is to check that code in a specific situation.

We can configure the breakpoint for being fired only if a condition returns true, pressing the right click at the breakpoint.

![](http://saulmm.github.io/resources/studio/conditional_breakpoint.gif)

The input will open the completion dialog with the context of the breakpoint while typing.

<br>
### Force an state

When a breakpoint is fired, we usually check the state of the execution. Instead just read we can also write and force the state to enable a certain condition of our code.

![](http://saulmm.github.io/resources/studio/force_state.gif)

With the action _evaulate expression_ (also from the watches window) in adition to check the states of certain variables we can also write the desired value on them.

<br>
### Different types of breakpoints

Unsetting the button _suspend_ tick in the breakpoint configuration dialog with a right click on a breakpoint, we could enable the option _Log message to console_.

Messages will be print under the tab _console_ at the _debug_ tool window (⌘ + 5).

![](http://saulmm.github.io/resources/studio/breakpoint_types.gif)

Besides, we could print _logs_ with an specific expression, which can be set at the same dialog under the _Log Evaluated Expression_ option. Once again it will suggest sentences of your context with the completion dialog while you are typing the expression.

<br>
## Conclusion 

Print the [cheatsheet](https://www.jetbrains.com/idea/docs/IntelliJIDEA_ReferenceCard_Mac.pdf) and hang it to your wall in order to have something to look during your gradle build times (You always could read your code btw! :D). 

![](http://saulmm.github.com/resources/studio/wall.jpg)


<br>
## References:

[No tabs in IntelliJ Idea][1] - *Hadi Hadiri*

[IntelliJ IDEA Tips and Tricks][2] - *Hadi Hadiri*

[The experts' guide to Android development tools][3] - *Google I/O '16*

[Android Studio for Experts][4] - *Android Summit '16*

[Android studio tips of the day roundups (all of them)][5] - *Philippe Breault*

[What's new in Android][10]

[Dev tips][13] - *Sebastiano Poggi*

[1]: http://hadihariri.com/2014/06/24/no-tabs-in-intellij-idea/

[2]: https://vimeo.com/138847553

[3]: https://www.youtube.com/watch?v=hHnTIMjd1Y8

[4]: https://www.youtube.com/watch?v=Y2GC6P5hPeA

[5]: http://www.developerphil.com/android-studio-tips-of-the-day-roundup-6/

[6]: https://www.jetbrains.com/help/idea/2016.1/navigating-through-the-source-code.html

[7]: https://www.jetbrains.com/

[8]: https://www.jetbrains.com/idea/

[9]: http://www.genbetadev.com/desarrollo-aplicaciones-moviles/android-studio-2-2-lleva-el-desarrollo-de-android-a-un-nuevo-nivel

[10]: http://tools.android.com/recent/androidstudio22preview1available

[11]: http://tools.android.com/tech-docs/layout-editor

[12]: https://www.youtube.com/watch?v=B08iLAtS3AQ

[13]: https://medium.com/sebs-top-tips

[14]: https://developer.android.com/studio/index.html


