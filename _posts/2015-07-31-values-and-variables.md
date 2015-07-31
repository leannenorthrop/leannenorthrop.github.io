---
layout: post
title: Values &amp; Variables &amp; All the Rest
tags: [scala, learning_scala]
---
Scala differs from Java when declaring variables:

<pre><code class="java hljs">// Java 
final int readOnlyInt = 1;
String variableString = "Hello";
variableString = "New String Value"
</code></pre>

<pre><code class="scala hljs">// Scala
val readOnlyInt = 1
var variableString = "Hello"
variableString = "New String Value"
</code></pre>

As you can see Scala is able to infer the type of variables and values but they are easily included in declarations like so:

<pre><code class="scala hljs">// Scala
val readOnlyInt : Int = 1
var variableString : String = "Hello"
variableString = "New String Value"
</code></pre>

A good practice is to use read-only values where ever possible to prevent bugs caused by mutability.

Note that type inference is based on local scopes and not globally, in other words method parameter types can not be inferred from the types at call-site. 

<div class="text-info">Scala strives for succinctness and allows semi-colons to be omitted. Everything until the end of the line is treated as part of the same expression unless the line is broken at an operator and continues on the next line.</div>

## Types
Everything in Scala is an object. Unlike Java primitive types are not supported (but are used under the covers when it's efficient to do so). 

<pre><code class="scala hljs">// Scala
val anInt : Int = 1
val aDouble : Double = 2.0
val aFloat : Float = 5.0f
val aString : String = "Hello"
val aCharacter : Char = 'A'
val aBoolean : Boolean = true
val aRange = 1 to 10
val anotherRange = 1 until 10 // Range(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val yetAnotherRange = 1 to 10 by 4 // Range(1, 2, 3, 4, 5, 6, 7, 8, 9)
val aCharacterRange = 'a' to 'z' by 5 // NumericRange(a, f, k, p, u, z)

</code></pre>