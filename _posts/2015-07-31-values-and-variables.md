---
layout: post
title: Values &amp; Variables &amp; All the Rest
tags: [scala, learning_scala]
blogpost: true
---
Scala differs from Java when declaring variables:

<pre><code class="java hljs"><span class="hljs-comment">// Java </span>
<span class="hljs-keyword">final</span> <span class="hljs-keyword">int</span> readOnlyInt = <span class="hljs-number">1</span>;
String variableString = <span class="hljs-string">"Hello"</span>;
variableString = <span class="hljs-string">"New String Value"</span>
</code></pre>

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">readOnlyInt</span> =</span> <span class="hljs-number">1</span>
<span class="hljs-keyword">var</span> variableString = <span class="hljs-string">"Hello"</span>
variableString = <span class="hljs-string">"New String Value"</span>
</code></pre>

As you can see Scala is able to infer the type of variables and values but they are easily included in declarations like so:

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">readOnlyInt</span> :</span> <span class="hljs-type">Int</span> = <span class="hljs-number">1</span>
<span class="hljs-keyword">var</span> variableString : <span class="hljs-type">String</span> = <span class="hljs-string">"Hello"</span>
variableString = <span class="hljs-string">"New String Value"</span>
</code></pre>

A good practice is to use read-only values where ever possible to prevent bugs caused by mutability.

Note that type inference is based on local scopes and not globally, in other words method parameter types can not be inferred from the types at call-site. 

<div class="text-info">Scala strives for succinctness and allows semi-colons to be omitted. Everything until the end of the line is treated as part of the same expression unless the line is broken at an operator and continues on the next line.</div>

## Types
Everything in Scala is an object. Unlike Java primitive types are not supported (but are used under the covers when it's efficient to do so). 

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">anInt</span> :</span> <span class="hljs-type">Int</span> = <span class="hljs-number">1</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aDouble</span> :</span> <span class="hljs-type">Double</span> = <span class="hljs-number">2.0</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aFloat</span> :</span> <span class="hljs-type">Float</span> = <span class="hljs-number">5.0</span>f
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aString</span> :</span> <span class="hljs-type">String</span> = <span class="hljs-string">"Hello"</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aMultiLineString</span> :</span> <span class="hljs-type">String</span> = <span class="hljs-string">"""This is a multiple
line
string."""</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aCharacter</span> :</span> <span class="hljs-type">Char</span> = '<span class="hljs-type">A</span>'
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aBoolean</span> :</span> <span class="hljs-type">Boolean</span> = <span class="hljs-literal">true</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aRange</span> =</span> <span class="hljs-number">1</span> to <span class="hljs-number">10</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">anotherRange</span> =</span> <span class="hljs-number">1</span> until <span class="hljs-number">10</span> <span class="hljs-comment">// Range(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">yetAnotherRange</span> =</span> <span class="hljs-number">1</span> to <span class="hljs-number">10</span> by <span class="hljs-number">4</span> <span class="hljs-comment">// Range(1, 2, 3, 4, 5, 6, 7, 8, 9)</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aCharacterRange</span> =</span> 'a' to 'z' by <span class="hljs-number">5</span> <span class="hljs-comment">// NumericRange(a, f, k, p, u, z)</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">aTuple</span> :</span> (<span class="hljs-type">String</span>, <span class="hljs-type">Int</span>, <span class="hljs-type">Double</span>) = (<span class="hljs-string">"Dog"</span>, <span class="hljs-number">12</span>, <span class="hljs-number">4.5</span>)
println(aTuple._1) <span class="hljs-comment">// Prints: Dog</span>
println(aTuple._3) <span class="hljs-comment">// Prints: 4.5</span>
</code></pre>

### Strings

#### Interpolation

Strings may be interpolated by prefixing them with an s and using the ${} to reference values or evaluate expressions and using % for specifying format flags:

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">dollars</span> =</span> <span class="hljs-number">141</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">str</span> =</span> f<span class="hljs-string">"${dollars} $$${dollars} $$${dollars}%.2f"</span>
println(str) <span class="hljs-comment">// Prints: 141 $141 $141.00</span>

<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">productName</span> =</span> <span class="hljs-string">"Apple"</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">productPrice</span> =</span> <span class="hljs-number">2</span>
println(f<span class="hljs-string">"${productName}%-20s${productPrice}"</span>)
</code></pre>

See [Formatter](http://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html) for details on flags.

#### Formatted

Strings may use the printf style formatting by prefixing them with an f and using the $ character to reference values or evaluate expressions:
<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">name</span> =</span> <span class="hljs-string">"Bruno"</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">str</span> =</span> s<span class="hljs-string">"Say hello to $name"</span>
</code></pre>

#### Multi-Line
Multiple line strings use triple double quotes but may optionally use the | with the stripMargin method:
<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">str</span> =</span> <span class="hljs-string">"""A very long,
            | long
            | long line."""</span>.stripMargin
</code></pre>
