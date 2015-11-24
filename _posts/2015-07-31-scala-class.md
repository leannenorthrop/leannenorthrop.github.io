---
layout: post
title: Scala Classes ~ The Basics
tags: [scala, learning_scala]
blogpost: true
---
Scala differs from Java in that the entire body of the class between the curly braces is considered part of a primary constructor, where the arguments to the constructor are given immediately after the class name. Methods are defined using the `def` keyword and the last expression of the method is used as the return value.

<pre><code class="java hljs"><span class="hljs-comment">// Java</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span> </span>{
	<span class="hljs-comment">// Constructor</span>
	MyClass(<span class="hljs-keyword">int</span> someParam) {
		<span class="hljs-comment">// do some set up here</span>
	}

	<span class="hljs-function">String <span class="hljs-title">doSomethingToString</span><span class="hljs-params">(String name)</span> </span>{
		<span class="hljs-keyword">return</span> <span class="hljs-string">"Hello "</span> + name;
	}
}</code></pre>

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span>(</span>someParam:<span class="hljs-type">Int</span>) {
	<span class="hljs-comment">// do some set up here</span>

	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = {
		<span class="hljs-string">"Hello "</span> + name
	}
}</code></pre>

## Constructors
Classes which do not take parameters can be instantiated with requiring the argument list:

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span>(</span>) {
	<span class="hljs-comment">// do some set up here</span>

	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = {
		<span class="hljs-string">"Hello "</span> + name
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myClass</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">MyClass</span>
myClass.doSomethingToString(<span class="hljs-string">"there"</span>)

<span class="hljs-comment">// or alternatively more succinctly </span>
<span class="hljs-type">MyClass</span>.doSomethingToString(<span class="hljs-string">"there"</span>)
</code></pre>

It's good to remember that the entire body of a Scala class is the primary constructor for the class. 

## Methods
Methods comprising of a single expression can use the simpler syntax (avoiding the use of curly braces):

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span>(</span>someParam:<span class="hljs-type">Int</span>) {
	<span class="hljs-comment">// do some set up here</span>

	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = <span class="hljs-string">"Hello "</span> + name
}</code></pre>

Names of methods can be any printable character or characters except keywords. Backticks can be used around method names to allow for whitespace or to escape Java method names where the method name is a keyword.

### Optional Parenthesis
Methods without parameters may be called with or without the parenthesis as can methods with single parameters.

### Default Values &amp; Named Arguments
Default parameter values can be supplied to methods and constructors by appending the value to the parameter declaration with =. e.g.

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span> = <span class="hljs-string">"Leanne"</span>) : <span class="hljs-type">String</span> = <span class="hljs-string">"Hello "</span> + name
</code></pre>

If all parameters have default values you can omit the () altogether or specify just the values requiring non-default values e.g.

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span> = <span class="hljs-string">"Leanne"</span>, msg : <span class="hljs-type">String</span> = <span class="hljs-string">"Hello "</span>) : <span class="hljs-type">String</span> = msg + name
...
doSomethingToString(msg=<span class="hljs-string">"Goodbye "</span>)
</code></pre>

In the example above `doSomethingToString(msg="Goodbye ")` demonstrates the named argument feature of Scala which is particularly useful for long parameter lists, or in case class copy method to change one or two values on the fly.

### Thunks, Lazy &amp; By Name 
In addition to calling method with parameters by name it is possible to lazily evaluate parameters. Usually this is done by passing in a zero-argument function that returns some value like:

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LazyClass</span>(</span>) {
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">thunks</span>(</span>someLazyParameter: () =&gt; <span class="hljs-type">Int</span>) : <span class="hljs-type">Int</span> = {
		<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">evaluatedInt</span> =</span> someLazyParameter()
		evaluatedInt * <span class="hljs-number">5</span>
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">LazyClass</span>().thunks(() =&gt; <span class="hljs-number">12</span>)
println(result) <span class="hljs-comment">// Prints: 60</span>
</code></pre>

However Scala allows this to be shorted like so:

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LazyClass</span>(</span>) {
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">thunks</span>(</span>someLazyParameter: =&gt; <span class="hljs-type">Int</span>) : <span class="hljs-type">Int</span> = {
		<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">evaluatedInt</span> =</span> someLazyParameter
		evaluatedInt * <span class="hljs-number">5</span>
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">LazyClass</span>().thunks(<span class="hljs-number">12</span>)
println(result)</code></pre>

Values and variables can also be declared using the `lazy` keyword to ensure they are not initialised until the value is used.

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LazyClass</span>(</span>) {
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">thunks</span>(</span>someLazyParameter: =&gt; <span class="hljs-type">Int</span>) : <span class="hljs-type">Int</span> = {
		<span class="hljs-keyword">lazy</span> <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">evaluatedInt</span> =</span> someLazyParameter
		evaluatedInt * <span class="hljs-number">5</span>
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">LazyClass</span>().thunks(<span class="hljs-number">12</span>)
println(result)</code></pre>

### Method Return Types
Method return types can be omitted as Scala is able to infer the type from the last expression in the method, however this can throw up some errors or cause confusion so it's best to always declare the return type as a way of generating self-documenting code. Also note that recursive methods must declare return types.

### Variable Argument List
Variadic parameters are supported using a * after the type and result in array:

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span>(</span>someParam:<span class="hljs-type">Int</span>) {
	<span class="hljs-comment">// do some set up here</span>

	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToStrings</span>(</span>names : <span class="hljs-type">String</span>*) : <span class="hljs-type">String</span> = {
		<span class="hljs-string">"Hello "</span> + names.mkString(<span class="hljs-string">" "</span>)
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myClass</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">MyClass</span>(<span class="hljs-number">1</span>)
println(myClass.doSomethingToStrings(<span class="hljs-string">"a"</span>, <span class="hljs-string">"b"</span>, <span class="hljs-string">"c"</span>))
<span class="hljs-comment">// Prints: Hello a b c</span></code></pre>

The :_* is used instead of * to convert the list of parameters into a sequence.

### Multiple Argument Lists
Scala supports multiple argument lists and are useful when the last argument list takes a single function parameter:

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span>(</span>) {
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">multipleArgumentLists</span>(</span>name : <span class="hljs-type">String</span>)(s:<span class="hljs-type">String</span> =&gt; <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = {
		<span class="hljs-string">"Hello "</span> + s(name)
	}
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myClass</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">MyClass</span>()
println(myClass.multipleArgumentLists(<span class="hljs-string">"there"</span>)(_.toUpperCase))
<span class="hljs-comment">// Prints: Hello THERE</span>

<span class="hljs-comment">// Alternatively</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">s</span> =</span> myClass.multipleArgumentLists(<span class="hljs-string">"there"</span>){_.toUpperCase}
</code></pre>

## Static Methods
Scala doesn't support static methods in the same way as Java but instead borrows implements single objects (borrowed from the singleton design pattern) which is a useful construct for holding utility methods and the like:

<pre><code class="java hljs"><span class="hljs-comment">// Java</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyClass</span> </span>{
	<span class="hljs-function"><span class="hljs-keyword">static</span> String <span class="hljs-title">doSomethingToString</span><span class="hljs-params">(String name)</span> </span>{
		<span class="hljs-keyword">return</span> <span class="hljs-string">"Hello "</span> + name;
	}
}</code></pre>

<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">MyClass</span>{</span>
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doSomethingToString</span>(</span>name : <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = {
		<span class="hljs-string">"Hello "</span> + name
	}
}</code></pre>

## Infix
If a method takes a single parameter the full stop and parenthesis are not required:

<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Multiplier</span>(</span>i:<span class="hljs-type">Int</span>) {
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">x</span>(</span>j:<span class="hljs-type">Int</span>): <span class="hljs-type">Int</span> = {
		i * j
	}
}

<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">m2</span> =</span> <span class="hljs-type">Multiplier</span>(<span class="hljs-number">2</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">something</span> =</span> m2 x <span class="hljs-number">3</span> <span class="hljs-comment">// Use infix syntax</span>
println(something) <span class="hljs-comment">// Prints 6</span>
</code></pre>

In this way new operators and DSLs can be easily created.

## Abstract Classes
Same as in Java:
<pre><code class="scala hljs"><span class="hljs-comment">// Scala</span>
<span class="hljs-keyword">abstract</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyAbstractClass</span>{</span>
...
}</code></pre>

### Sealed Class Hierarchies

The `sealed` keyword in Scala is used mainly on base abstract classes or base traits and indicates that all subclasses must be declared in the same file. 