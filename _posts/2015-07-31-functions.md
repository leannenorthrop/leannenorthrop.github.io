---
layout: post
title: Functions ~ The Basics
tags: [scala, learning_scala, functions]
blogpost: true
---
Scala has support for functions as first-class values in that functions can be assigned to variables, and passed and returned from other functions.

<pre><code style="scala" class="hljs scala"><span class="hljs-comment">//Scala</span>
<span class="hljs-comment">// Function Literal</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myFunction</span> =</span> (s:<span class="hljs-type">String</span>) =&gt; s.toUpperCase()

<span class="hljs-comment">// Call myFunction and print result</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result</span> =</span> myFunction(<span class="hljs-string">"Leanne"</span>)
println(result) <span class="hljs-comment">// Prints: LEANNE</span>

<span class="hljs-comment">// Pass myFunction to another function and print result</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">cart</span> =</span> <span class="hljs-type">List</span>(<span class="hljs-string">"Pear"</span>, <span class="hljs-string">"Apple"</span>, <span class="hljs-string">"Bannana"</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result2</span> =</span> cart.map(myFunction)
println(result2) <span class="hljs-comment">// Prints: List(PEAR, APPLE, BANNANA)</span>

<span class="hljs-comment">// Functional Literal</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">cart</span> =</span> <span class="hljs-type">List</span>(<span class="hljs-string">"Pear"</span>, <span class="hljs-string">"Apple"</span>, <span class="hljs-string">"Bannana"</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result3</span> =</span> cart.map((s : <span class="hljs-type">String</span>) =&gt; s.toUpperCase())
println(result3) <span class="hljs-comment">// Prints: List(PEAR, APPLE, BANNANA)</span>

<span class="hljs-comment">// Simpler Functional Literal</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">cart</span> =</span> <span class="hljs-type">List</span>(<span class="hljs-string">"Pear"</span>, <span class="hljs-string">"Apple"</span>, <span class="hljs-string">"Bannana"</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result3</span> =</span> cart.map(_.toUpperCase()) <span class="hljs-comment">// Use the _ character to refer to the first parameter. </span>
println(result3) <span class="hljs-comment">// Prints: List(PEAR, APPLE, BANNANA)</span>
</code></pre>

When a function like `map` takes a function as a parameter it is called a higher-order function. When the return type is Unit (Scala equivalent to Java's void) the function is not pure but instead only causes side-effects.

## Nested Functions

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Math</span> {</span>
  <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">!</span>(</span>i:<span class="hljs-type">Int</span>):<span class="hljs-type">Long</span> = {
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">factorial</span>(</span>i:<span class="hljs-type">Int</span>, acc:<span class="hljs-type">Int</span>):<span class="hljs-type">Long</span> = {
      <span class="hljs-keyword">if</span> (i &lt;= <span class="hljs-number">1</span>) acc
      <span class="hljs-keyword">else</span> factorial(i-<span class="hljs-number">1</span>, i * acc)
    }
    factorial(i,<span class="hljs-number">1</span>)
  }
}
println(<span class="hljs-type">Math</span> ! <span class="hljs-number">4</span>) <span class="hljs-comment">// Prints: 24</span>
</code></pre>

### Tail Recursion 
To ensure that Scala compiler implements tail-call optimisation use the `@tailrec` annotation which will cause the compiler to print a warning if there is an error. This will prevent stack overflow.

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Math</span> {</span>
  <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">!</span>(</span>i:<span class="hljs-type">Int</span>):<span class="hljs-type">Long</span> = {
    <span class="hljs-annotation">@tailrec</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">factorial</span>(</span>i:<span class="hljs-type">Int</span>, acc:<span class="hljs-type">Int</span>):<span class="hljs-type">Long</span> = {
      <span class="hljs-keyword">if</span> (i &lt;= <span class="hljs-number">1</span>) acc
      <span class="hljs-keyword">else</span> factorial(i-<span class="hljs-number">1</span>, i * acc)
    }
    factorial(i,<span class="hljs-number">1</span>)
  }
}
println(<span class="hljs-type">Math</span> ! <span class="hljs-number">4</span>) <span class="hljs-comment">// Prints: 24</span>
</code></pre>

## Partial Functions
Not to be confused with partially applied functions, partial functions are those which implement functionality for only a specific sub-group of the input. Only `case` clauses are allowed and must be contained within a pair of curly braces. In Scala it must take a single parameter and can take a literal form:

<pre><code class="scala hljs"><span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myPartialFunction</span> :</span> <span class="hljs-type">PartialFunction</span>[<span class="hljs-type">String</span>,<span class="hljs-type">String</span>]= {
    <span class="hljs-keyword">case</span> <span class="hljs-string">"hello"</span> =&gt; <span class="hljs-string">"found hello"</span>
    <span class="hljs-keyword">case</span> <span class="hljs-string">"goodbye"</span> =&gt; <span class="hljs-string">"found goodbye"</span>
}
println(myPartialFunction(<span class="hljs-string">"hello"</span>)) <span class="hljs-comment">// Prints: found hello</span>
println(myPartialFunction(<span class="hljs-string">"goodbye"</span>)) <span class="hljs-comment">// Prints: found good-by</span>
println(myPartialFunction(<span class="hljs-string">"thinking"</span>)) <span class="hljs-comment">/* Prints:
scala.MatchError: thinking (of class java.lang.String)
  at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:253)
  at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:251)
  ... elided*/</span>
</code></pre>

To prevent the MatchError a `isDefinedAt` method can be be used which return true if a MatchError will not be thrown.

<pre><code class="scala hljs"><span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aString</span> :</span> <span class="hljs-type">PartialFunction</span>[<span class="hljs-type">Any</span>,<span class="hljs-type">String</span>]= {
    <span class="hljs-keyword">case</span> <span class="hljs-string">"hello"</span> =&gt; <span class="hljs-string">"found string hello"</span>
    <span class="hljs-keyword">case</span> s:<span class="hljs-type">String</span> =&gt; <span class="hljs-string">"found string "</span> + s
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">anInt</span> :</span> <span class="hljs-type">PartialFunction</span>[<span class="hljs-type">Any</span>,<span class="hljs-type">String</span>]= {
    <span class="hljs-keyword">case</span> i:<span class="hljs-type">Int</span> =&gt; <span class="hljs-string">"found int "</span> + i
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">anything</span> :</span> <span class="hljs-type">PartialFunction</span>[<span class="hljs-type">Any</span>,<span class="hljs-type">String</span>]= {
    <span class="hljs-keyword">case</span> unexpected =&gt; <span class="hljs-string">"found unknown anything "</span> + unexpected
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">combined</span> =</span> aString orElse anInt orElse anything

println(combined(<span class="hljs-string">"hello"</span>)) <span class="hljs-comment">// Prints: found hello</span>
println(combined(<span class="hljs-string">"goodbye"</span>)) <span class="hljs-comment">// Prints: found good-by</span>
println(combined(<span class="hljs-number">1</span>)) <span class="hljs-comment">// Prints: found int 1</span>
println(combined(<span class="hljs-number">5.0</span>)) <span class="hljs-comment">// Prints: found unknown anything 5.0</span>
</code></pre>