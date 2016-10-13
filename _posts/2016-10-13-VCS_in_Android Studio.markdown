---
layout: post
permalink: vcs-android-studio
title: The VCS client of Android Studio
---

Nowadays, we can't imagine start a new software without a [version control system][5fb469a7], between the different options available for a VCS, Git, without any doubt, has become one of the most popular systems among others like Subversion or Mercurial.

However, **the learning curve** that this kind of tools requires is often **very high**, since the huge variety of features and options that a version control system includes make it complex and powerful at the same time.

Both for the most experimented developers and for the beginners is important to use **nice tools** or a well enough customized environment in order to deal with the VCS in a effective and quick way.

<br/>
## Comparisons

Between the benefits we can find using a version control system, a powerful one is to **compare files with their respective states over time**. To that end, [JetBrains][aec9d229] includes one of the most powerful and intuitive **_diff / merge_** tools.

A good use of these tools allows to speed up the daily work since these **comparisons are frequent** when we are modifying the code (adding, deleting or changing), besides reviewing it. Moreover, these **modification** are often **hard to represent**, so is not quite easy to find a good tool to show clearly these changes.

### IntelliJ as a diff, merge tool

Starting with version 2016 of IntelliJ IDEA, [JetBrains][aec9d229] has included an interesting feature called _**[Create command line launcher][7843d9cd]**_, which Android Studio has inherited in its [2.2][c9d1efd1] update.

![](http://saulmm.github.io/resources/cvs-studio/command_line_launcher.png)

This new feature allows the use of the _**diff / merge**_ tools used in IntelliJ or Android Studio,  **externally**. In such a way that we could use it from **command line** or from our **favorite third party VCS client**.

![](http://saulmm.github.io/resources/cvs-studio/external_diff.gif)

Therefore, among other interesting situations, we could solve a conflict or compare different files using these external tools, which is really handy. As alternatives, tools like [Kaleidoscope][8e5e7c90] or [Filemerge][3d57ed6e] are also oriented to this purpose.

The VCS external client that I usually use is [SourceTree][2ed978b9], by [Atlassian][5ac4ea11]. To setting the Android Studio **diff / merge** tools as the **default tool** of SourceTree, we only need to set the binary created by the IDE _(`/user/local/bin/studio` by default)_ in the settings section of [SourceTree][2ed978b9] **`Settings/Diff`**, setting the external tool as **other** along the proper parameters.

![](http://saulmm.github.io/resources/cvs-studio/sourcetree_conf.png)

<br/>
### Compare the current file with a branch

IntelliJ IDEA and Android Studio allow to compare the current open file in the editor with their respective version in a different branch. This may be interesting in various situations like reviewing code, verifying if a conflict has been solved properly, etc.

This feature, called _**Compare with branch**_, is available in the VCS menu or using _**find action**_ **( ⌘ + A )** typing part of the feature name.

After choosing **any branch** which exists in the VCS (both local and remote versions), immediately a **diff** tool will be shown in order to compare the changes between branches.

![](http://saulmm.github.io/resources/cvs-studio/compare_with_branch.gif)

<br/>
### Compare with a previous commit

The feature _**Compare with...**_ will allow us, using a dialog, to choose a commit made previously in order to compare the changes made from that point to the current state of the file.

![](http://saulmm.github.io/resources/cvs-studio/compare_with-history.gif)

If, instead, we want to show the history of _commits_ where the current file has been changing, we can use the feature **_Show history..._**, which will use a **fixed panel** shown at the bottom of the IDE to this purpose.

<br/>
## Handling the changes

Often, after modifying some files, may be useful to check which changes has been made from the last _commit_ (changes that have not been committed yet). The feature **_local changes_** will put the focus in a bottom panel showing all local modifications that have not been committed.

At the **_local changes_** panel we can perform some features without taking your hands off the keyboard:

### Show the _diff_ tool with the changes made from the latest commit - **( ⌘ + D )**
<br/>
![](http://saulmm.github.io/resources/cvs-studio/local_changes_diff.gif)
<br/>
### Revert changes - **⌥ + ⌘ + Z**

Furthermore, this feature can be used in more contexts outside the _log_, using revert **⌥ + ⌘ + Z** on any file, will show the revert dialog allowing to revert the most recent modifications done from the latest commit.

![](http://saulmm.github.io/resources/cvs-studio/local_changes_revert.gif)

**_Note_:** Normally, the dialogs shown in IntelliJ/Android Studio can be closed with the shortcut **tab + enter**.

<br/>
## Log of the VCS

Despite, personally, I'm more with third party VCS clients like [SourceTree][2ed978b9] (which it has a nice way for showing the _log_),  Android Studio and IntelliJ also implement this feature, accessible looking for the feature **_Show VCS log_** via find action or reaching it under the VCS menu.

![](http://saulmm.github.io/resources/cvs-studio/vcs_log.gif)

<br/>
As advantages, being integrated inside the IDE it leverages all the navigations skills of IntelliJ. Therefore, we can interact with the log in a very easy and quick way with the keyboard firing different actions.

If we select certain _commit_ we can access via **find action**_  **( ⇧ + ⌘ + A )** to interesting features related with the selected commit like _resets_, _checkout_, _diffs_ using **( ⌘ + D )**, etc.

![](http://saulmm.github.io/resources/cvs-studio/vcs_log_diff.gif)

<br>
### VCS operations popup **( ⌥ + V )**

We can show a dialog with the most commonly used operations working with a VCS like: _commit_, _branches_, _stash_, etc. Using the shortcut **( ⌥ + V )**.

![](http://saulmm.github.io/resources/cvs-studio/vcs_dialog.gif)

<br/>
## Committing

One of the key requirements using a VCS properly is to perform _commits_ **very frequently** properly. A commit is considered **atomic** when all the modifications are related with one change semantically. Besides, a nice commit should express what is doing with a clear, concise and descriptive title.

However, it could be cumbersome, in environments where a third party VCS or poor customized command line are used, perform commits in a quick way.

### Committing fast - **( ⌘ + K )** + **(tab + enter)**

Using the VCS client integrated in Android Studio or IntelliJ, utilizing the shortcut **( ⌘ + K )** + **(⇥ + enter)** we can have ready a commit in less than 5 seconds. Besides, there are a few interesting features that we can enable before committing, like code inspections, organize imports, etc.

![](http://saulmm.github.io/resources/cvs-studio/quick_commit.gif)

<br/>
## [GitHub][b65ae60d] integration

IntelliJ, Android Studio and probably among other JetBrains environments, we can take advantage of an  integration with [GitHub][b65ae60d], after setting our credentials we will be able to use some useful features to interact with the social coding platform.


### Get direct web links to the repository

Either from the file explorer or editing a file, the feature _**Open on GitHub**_, will open a **browser** with the remote version of that file. Moreover, if we run this feature with a **selection of text**, the same text will be highlighted in the link created with the [GitHub][b65ae60d] version.

![](http://saulmm.github.io/resources/cvs-studio/quick_commit.gif)

### Create Pull Requests

There is a way to **create pull requests** directly from the editor, using the feature **_Create Pull Request_** (as usual accessible via find action), the IDE will prompt a **dialog** asking for the **base branch** and the **candidate for merge**. After filling the form, a pull request will be created at [GitHub][b65ae60d].

![](http://saulmm.github.io/resources/cvs-studio/quick_commit.gif)

### Create Gist

Similarly, we can create private, **anonymous and public** [Gists](https://gist.github.com/) on  [GitHub][b65ae60d] from Android Studio with the feature **_Create Gist_**.

![](http://saulmm.github.io/resources/cvs-studio/github_gist.gif)

## References

[How to write a commit message][7bcdcf8a] - Chris Beams.

[Version Control Help][a9927f4d] - IntelliJ IDEA

[a9927f4d]: https://www.jetbrains.com/help/idea/2016.2/version-control.html "IntelliJ"
[7bcdcf8a]: http://chris.beams.io/posts/git-commit/ "How to write a commit message"
[c9d1efd1]: http://tools.android.com/recent/androidstudio22preview2available "Android Studio 2.2"
[aec9d229]: https://www.jetbrains.com/ "JetBrains"
[3d57ed6e]: https://developer.apple.com/xcode/features/ "Filemerge"
[8e5e7c90]: http://www.kaleidoscopeapp.com/ "kaleidoscope"
[7843d9cd]: https://www.jetbrains.com/help/idea/2016.2/running-intellij-idea-as-a-diff-or-merge-command-line-tool.html "Create command line launcher"
[5ac4ea11]: https://www.atlassian.com/ "atlassian"
[2ed978b9]: https://www.sourcetreeapp.com/ "SourceTree"
[b65ae60d]: https://github.com/ "Github"

  [5fb469a7]: https://en.wikipedia.org/wiki/Version_control "VCS"
