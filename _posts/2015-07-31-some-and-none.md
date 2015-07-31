---
layout: post
title: Scala Some &amp; None
tags: [scala, learning_scala]
---
Although Scala, like Java, supports Exceptions (but not checked exceptions) an alternative error handling mechanism is provided through the the Option type. This type has two values: Some[T] and None for some parameterised type T. It's useful particularly when an 'unknown' value should be returned from a method or function. Rather than using null it's possible to instead return a None, and for valid return values Some[T]. Option has a method `getOrElse` which if the option is none will return the provided value.

<pre><code class="scala">val something = Some("Hello")
println(something.getOrElse("Goodbye")) // Prints: Hello
val nothing = None
println(nothing.getOrElse("Goodbye")) // Prints: Goodbye
</code></pre>