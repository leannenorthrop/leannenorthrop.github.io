---
layout: post
title: Starting Simply With SBT (Scala Build Tool) and Scala Console
tags: [learning_scala,sbt]
blogpost: true
---
Understanding Scala Build Tool, or SBT, is quite straight-forward coming from Java's build tool Maven as it is able to leverage Maven repositories directly via Ivy, has plugins and multi-project builds etc. but also offers a range of new options such as run tests automatically on source file changes. 

For a complete beginner it is best to think of SBT as a command that saves time when compiling code that depends on libraries stored in remote locations, automatically testing and gathering your code into an application or a set of libraries. Although you can run SBT directly in any directory it's best to set-up some basic information for your project. In the root directory of your project create a new file called `build.sbt` which should contain something like:

<pre><code class="scala hljs">name := <span class="hljs-string">"My Project Name"</span>

version := <span class="hljs-string">"1.0"</span>

scalaVersion := <span class="hljs-string">"2.11.7"</span>
</code></pre>

For the records this is known as a 'bare' sbt file since it consists of only Setting expressions. There are [other types build.sbt files](http://www.scala-sbt.org/0.13/tutorial/Bare-Def.html) that are used for larger projects.

If your project requires additional jar libraries use the `resolvers` and `libraryDependencies` options to set the remote repository location and library versions respectively, and add them like so:

<pre><code class="scala hljs">name := <span class="hljs-string">"My Project Name"</span>

version := <span class="hljs-string">"1.0"</span>

scalaVersion := <span class="hljs-string">"2.11.7"</span>

resolvers += <span class="hljs-string">"akka-repo"</span> at <span class="hljs-string">"http://akka.io/repository"</span>

libraryDependencies += <span class="hljs-string">"com.typesafe.akka"</span> % <span class="hljs-string">"akka-actor_2.11"</span> % <span class="hljs-string">"2.3.12"</span>
</code></pre>

As your project grows you may wish to add one or more [plugins](http://www.scala-sbt.org/0.13/tutorial/Using-Plugins.html) but more on that another time as this is enough SBT setup for learning Scala via sbt console.

## SBT Scala Console
Just typing `sbt` command into the command prompt in your root directory will begin a SBT session which offers a range of commands. Type `help` to see the list of commands and `exit` to return to the command prompt. One very useful command is the `console` command which starts a Scala session which can be used to try out different Scala expressions or snippets of code. 

Although IDE's such as IntelliJ offer [worksheet functionality](https://confluence.jetbrains.com/display/IntelliJIDEA/Working+with+Scala+Worksheet) I enjoy using a Max OSX terminal session side-by-side with Sublime as I'm currently working on an older lap-top without much horse-power. Scripts can be loaded into the SBT Scala console using the load command e.g. `:load dir/to/script.sc`. 

To try this out for yourself you can create in your root directory a new directory to store script files in e.g. `mkdir scripts` and create a new script file called for.sc which should contain:

<pre><code class="scala hljs"><span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">bools</span> =</span> <span class="hljs-type">Seq</span>(<span class="hljs-literal">true</span>, <span class="hljs-literal">false</span>, <span class="hljs-literal">true</span>, <span class="hljs-literal">true</span>)

<span class="hljs-keyword">for</span> (bool &lt;- bools) {
  bool <span class="hljs-keyword">match</span> {
    <span class="hljs-keyword">case</span> <span class="hljs-literal">true</span> =&gt; println(<span class="hljs-string">"Got heads"</span>)
    <span class="hljs-keyword">case</span> <span class="hljs-literal">false</span> =&gt; println(<span class="hljs-string">"Got tails"</span>)
  }
}
</code></pre>

Inside your running SBT console type:

<pre><code class="hljs xquery">&gt; console
[info] Starting scala interpreter...
[info] 
Welcome <span class="hljs-keyword">to</span> Scala version <span class="hljs-number">2.11</span>.<span class="hljs-number">7</span> (Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server VM, Java <span class="hljs-number">1.8</span>.<span class="hljs-number">0_51</span>).
Type <span class="hljs-keyword">in</span> expressions <span class="hljs-keyword">to</span> have them evaluated.
Type :help <span class="hljs-keyword">for</span> more information.

scala&gt; :load scripts/<span class="hljs-keyword">for</span>.sc
Loading scripts/matchbool.sc...
bools: Seq[Boolean] = List(<span class="hljs-literal">true</span>, <span class="hljs-literal">false</span>, <span class="hljs-literal">true</span>, <span class="hljs-literal">true</span>)
Got heads
Got tails
Got heads
Got heads

scala&gt;
</code></pre>

Other scripts can be loaded and run at this point or simply edit run.sc and re-run by using the up-arrow key to scroll through the command buffer without needing to exit either the Scala console or SBT itself.