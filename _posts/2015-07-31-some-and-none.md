---
layout: post
title: Scala Some &amp; None
tags: [scala, learning_scala]
blogpost: true
---
Although Scala, like Java, supports Exceptions (but not checked exceptions) an alternative error handling mechanism is provided through the the Option type. This type has two values: Some[T] and None for some parameterised type T. It's useful particularly when an 'unknown' value should be returned from a method or function. Rather than using null it's possible to instead return a None, and for valid return values Some[T]. Option has a method `getOrElse` which if the option is none will return the provided value.

<pre><code class="scala hljs"><span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">something</span> =</span> <span class="hljs-type">Some</span>(<span class="hljs-string">"Hello"</span>)
println(something.getOrElse(<span class="hljs-string">"Goodbye"</span>)) <span class="hljs-comment">// Prints: Hello</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">nothing</span> =</span> <span class="hljs-type">None</span>
println(nothing.getOrElse(<span class="hljs-string">"Goodbye"</span>)) <span class="hljs-comment">// Prints: Goodbye</span>
</code></pre>