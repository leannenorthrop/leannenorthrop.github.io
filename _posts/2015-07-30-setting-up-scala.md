---
layout: post
title: Setting Up Scala Environment
tags: [learning_scala,sbt,scala_docs]
blogpost: true
---
Although Typesafe's Activator tool has been developed to ease the pain of starting Scala projects I unfortunately started using it on a day when a central library repository was down and the majority of the templates intermittently failed to compile and run. In sheer frustration I tried searching for other information on basic set-up but found I was installing tool after tool, so for the records a really simple, straight-forward set-up consists of:

* [Java SDK 8+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Scala 2.11.7+](http://www.scala-lang.org/download/2.11.7.html). Also grab:
	* [off-line library documentation](http://downloads.typesafe.com/scala/2.11.7/scala-docs-2.11.7.zip), and 
	* [sources](https://github.com/scala/scala/archive/v2.11.7.tar.gz).
* [SBT](http://www.scala-sbt.org/download.html), Scala Build Tool for handling library dependencies, compilation, continuous testing, console and all other build tasks.
* Your favorite IDE e.g [IntelliJ](https://www.jetbrains.com/idea/download/) or text editor e.g. [Sublime](http://www.sublimetext.com/3)

Installing Java should automatically add the various tools to your OS Path but to ensure it is installed correctly open a new command prompt or terminal session, type `java -version` and you should have output similar to this:

<pre><code class="bash hljs">$ java -version
java version <span class="hljs-string">"1.8.0_51"</span>
Java(TM) SE Runtime Environment (build <span class="hljs-number">1.8.0_51-b16</span>)
Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server VM (build <span class="hljs-number">25.51-b03</span>, mixed mode)
</code></pre>

Next extract the Scala archives to a suitable, permanent home somewhere on your computer. This time you will need to manually add the full directory path to the bin directory to your OS Path. For instructions on how to do this on Windows see [here](http://www.computerhope.com/issues/ch000549.htm), and for Linux/Mac OSX add `export PATH=$PATH:/path/to/scala-bin-directory` to the .profile file in your home directory changing `/path/to/scala-bin-directory` to match your directory path e.g. `export PATH=$PATH:/Users/leanne/Development/Scala/scala-2.11.7/bin`. Open a **new** command prompt or terminal session, type `scala -version` and you should see output similar to:

<pre><code class="bash hljs">$ scala -version
Scala code runner version <span class="hljs-number">2.11.7</span> -- Copyright <span class="hljs-number">2002</span>-<span class="hljs-number">2013</span>, LAMP/EPFL
</code></pre>

Lastly, download and extract to a permanent home the build tool, SBT. Again you will need to manually add the full directory path to the bin directory to you OS Path. If you are on Linux or Mac OSX you will also need to make the sbt shell script executable like so:

<pre><code class="bash hljs">$ <span class="hljs-built_in">cd</span> sbt/bin
$ chmod +x sbt
</code></pre>

Options to the Java VM can be passed through using the `SBT_OPTS` environment variable e.g. `export SBT_OPTS="-XX:+CMSClassUnloadingEnabled"`. Once SBT is available on the OS Path you can start a new session anywhere by typing `sbt` at the command prompt and using the `exit` command to end e.g.

<pre><code class="bash hljs">$ sbt
Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server
[info] Set current project to desktop (in build file:/Users/leanne/Desktop/)
&gt; <span class="hljs-built_in">exit</span>
</code></pre>