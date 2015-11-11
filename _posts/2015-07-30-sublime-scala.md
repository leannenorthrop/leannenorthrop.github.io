---
layout: post
title: Sublime Scala
tags: [learning_scala,sublime]
blogpost: true
---
Yes, Scala is sublime for many users but in this post I wanted to document how to set up one of my favourite text editor, Sublime, in a simple way for learning Scala and leave an in-depth discussion on the merits of Scala to another time :-)

First of all grab a copy of [Sublime 3](http://www.sublimetext.com/3), extract and run. Sublime has a great package manager tool which you can install by using the `View > Show Console menu` and pasting the code snippet from [here](https://packagecontrol.io/installation) into the console. This adds new commands to the Command Palette (under the Tools menu) the most important of which is the `Package Control: Install Package` option where anything from code formatters to colour schemes can be installed.

I'm enjoying learning Scala using Scala script files but SBT will try to compile these files if given the `.scala` extension so I follow the convention of using a `.sc` extension instead. Sublime doesn't automatically recognise these .sc files as being in Scala syntax so to fix this open a scala script file and use the `View > Syntax > Open all with current extension as... > Scala`. The same can be used with .sbt files.