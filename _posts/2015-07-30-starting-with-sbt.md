---
layout: post
title: Starting Simply With SBT (Scala Build Tool) and Scala Console
tags: [learning_scala,sbt]
---
Understanding Scala Build Tool, or SBT, is quite straight-forward coming from Java's build tool Maven as it is able to leverage Maven repositories directly via Ivy, has plugins and multi-project builds etc. but also offers a range of new options such as run tests automatically on source file changes. 

For a complete beginner it is best to think of SBT as a command that saves time when compiling code that depends on libraries stored in remote locations, automatically testing and gathering your code into an application or a set of libraries. Although you can run SBT directly in any directory it's best to set-up some basic information for your project. In the root directory of your project create a new file called `build.sbt` which should contain something like:

<pre><code class="scala">name := "My Project Name"

version := "1.0"

scalaVersion := "2.11.7"
</code></pre>

For the records this is known as a 'bare' sbt file since it consists of only Setting expressions. There are [other types build.sbt files](http://www.scala-sbt.org/0.13/tutorial/Bare-Def.html) that are used for larger projects.

If your project requires additional jar libraries use the `resolvers` and `libraryDependencies` options to set the remote repository location and library versions respectively, and add them like so:

<pre><code class="scala">name := "My Project Name"

version := "1.0"

scalaVersion := "2.11.7"

resolvers += "akka-repo" at "http://akka.io/repository"

libraryDependencies += "com.typesafe.akka" % "akka-actor_2.11" % "2.3.12"
</code></pre>

As your project grows you may wish to add one or more [plugins](http://www.scala-sbt.org/0.13/tutorial/Using-Plugins.html) but more on that another time as this is enough SBT setup for learning Scala via sbt console.

<h2>SBT Scala Console</h2>
Just typing `sbt` command into the command prompt in your root directory will begin a SBT session which offers a range of commands. Type `help` to see the list of commands and `exit` to return to the command prompt. One very useful command is the `console` command which starts a Scala session which can be used to try out different Scala expressions or snippets of code. 

Although IDE's such as IntelliJ offer [worksheet functionality](https://confluence.jetbrains.com/display/IntelliJIDEA/Working+with+Scala+Worksheet) I enjoy using a Max OSX terminal session side-by-side with Sublime as I'm currently working on an older lap-top without much horse-power. Scripts can be loaded into the SBT Scala console using the load command e.g. `:load dir/to/script.sc`. 

To try this out for yourself you can create in your root directory a new directory to store script files in e.g. `mkdir scripts` and create a new script file called for.sc which should contain:

<pre><code class="scala">val bools = Seq(true, false, true, true)

for (bool <- bools) {
	bool match {
		case true => println("Got heads")
		case false => println("Got tails")
	}
}
</code></pre>

Inside your running SBT console type:

<pre><code>&gt; console
[info] Starting scala interpreter...
[info] 
Welcome to Scala version 2.11.7 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_51).
Type in expressions to have them evaluated.
Type :help for more information.

scala&gt; :load scripts/for.sc
Loading scripts/matchbool.sc...
bools: Seq[Boolean] = List(true, false, true, true)
Got heads
Got tails
Got heads
Got heads

scala&gt;
</code></pre>

Other scripts can be loaded and run at this point or simply edit run.sc and re-run by using the up-arrow key to scroll through the command buffer without needing to exit either the Scala console or SBT itself.