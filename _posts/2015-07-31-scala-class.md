---
layout: post
title: Scala Classes ~ The Basics
tags: [scala, learning_scala]
---
Scala differs from Java in that the entire body of the class between the curly braces is considered part of a primary constructor, where the arguments to the constructor are given immediately after the class name. Methods are defined using the `def` keyword and the last expression of the method is used as the return value.

<pre><code class="java hljs">// Java
class MyClass {
	// Constructor
	MyClass(int someParam) {
		// do some set up here
	}

	String doSomethingToString(String name) {
		return "Hello " + name;
	}
}</code></pre>

<pre><code class="scala hljs">// Scala
class MyClass(someParam:Int) {
	// do some set up here

	def doSomethingToString(name : String) : String = {
		"Hello " + name
	}
}</code></pre>

## Constructors
Classes which do not take parameters can be instantiated with requiring the argument list:

<pre><code class="scala hljs">// Scala
class MyClass() {
	// do some set up here

	def doSomethingToString(name : String) : String = {
		"Hello " + name
	}
}
val myClass = new MyClass
myClass.doSomethingToString("there")

// or alternatively more succinctly 
MyClass.doSomethingToString("there")
</code></pre>

It's good to remember that the entire body of a Scala class is the primary constructor for the class. 

## Methods
Methods comprising of a single expression can use the simpler syntax (avoiding the use of curly braces):

<pre><code class="scala hljs">// Scala
class MyClass(someParam:Int) {
	// do some set up here

	def doSomethingToString(name : String) : String = "Hello " + name
}</code></pre>

Names of methods can be any printable character or characters except keywords. Backticks can be used around method names to allow for whitespace or to escape Java method names where the method name is a keyword.

### Optional Parenthesis
Methods without parameters may be called with or without the parenthesis as can methods with single parameters.

### Default Values &amp; Named Arguments
Default parameter values can be supplied to methods and constructors by appending the value to the parameter declaration with =. e.g.

<pre><code class="scala hljs">// Scala
def doSomethingToString(name : String = "Leanne") : String = "Hello " + name
</code></pre>

If all parameters have default values you can omit the () altogether or specify just the values requiring non-default values e.g.

<pre><code class="scala hljs">// Scala
def doSomethingToString(name : String = "Leanne", msg : String = "Hello ") : String = msg + name
...
doSomethingToString(msg="Goodbye ")
</code></pre>

In the example above `doSomethingToString(msg="Goodbye ")` demonstrates the named argument feature of Scala which is particularly useful for long parameter lists, or in case class copy method to change one or two values on the fly.

### Thunks, Lazy &amp; By Name 
In addition to calling method with parameters by name it is possible to lazily evaluate parameters. Usually this is done by passing in a zero-argument function that returns some value like:

<pre><code class="scala">class LazyClass() {
	def thunks(someLazyParameter: () => Int) : Int = {
		val evaluatedInt = someLazyParameter()
		evaluatedInt * 5
	}
}
val result = new LazyClass().thunks(() => 12)
println(result) // Prints: 60
</code></pre>

However Scala allows this to be shorted like so:

<pre><code class="scala">class LazyClass() {
	def thunks(someLazyParameter: => Int) : Int = {
		val evaluatedInt = someLazyParameter
		evaluatedInt * 5
	}
}
val result = new LazyClass().thunks(12)
println(result)</code></pre>

Values and variables can also be declared using the `lazy` keyword to ensure they are not initialised until the value is used.

<pre><code class="scala">class LazyClass() {
	def thunks(someLazyParameter: => Int) : Int = {
		lazy val evaluatedInt = someLazyParameter
		evaluatedInt * 5
	}
}
val result = new LazyClass().thunks(12)
println(result)</code></pre>

### Method Return Types
Method return types can be omitted as Scala is able to infer the type from the last expression in the method, however this can throw up some errors or cause confusion so it's best to always declare the return type as a way of generating self-documenting code. Also note that recursive methods must declare return types.

### Variable Argument List
Variadic parameters are supported using a * after the type and result in array:

<pre><code class="scala hljs">// Scala
class MyClass(someParam:Int) {
	// do some set up here

	def doSomethingToStrings(names : String*) : String = {
		"Hello " + names.mkString(" ")
	}
}
val myClass = new MyClass(1)
println(myClass.doSomethingToStrings("a", "b", "c"))
// Prints: Hello a b c</code></pre>

### Multiple Argument Lists
Scala supports multiple argument lists and are useful when the last argument list takes a single function parameter:

<pre><code class="scala hljs">// Scala
class MyClass() {
	def multipleArgumentLists(name : String)(s:String => String) : String = {
		"Hello " + s(name)
	}
}
val myClass = new MyClass()
println(myClass.multipleArgumentLists("there")(_.toUpperCase))
// Prints: Hello THERE

// Alternatively
val s = myClass.multipleArgumentLists("there"){_.toUpperCase}
</code></pre>

## Static Methods
Scala doesn't support static methods in the same way as Java but instead borrows implements single objects (borrowed from the singleton design pattern) which is a useful construct for holding utility methods and the like:

<pre><code class="java hljs">// Java
class MyClass {
	static String doSomethingToString(String name) {
		return "Hello " + name;
	}
}</code></pre>

<pre><code class="scala hljs">// Scala
object MyClass{
	def doSomethingToString(name : String) : String = {
		"Hello " + name
	}
}</code></pre>

## Infix
If a method takes a single parameter the full stop and parenthesis are not required:

<pre><code class="scala">case class Multiplier(i:Int) {
	def x(j:Int): Int = {
		i * j
	}
}

val m2 = Multiplier(2)
val something = m2 x 3 // Use infix syntax
println(something) // Prints 6
</code></pre>

In this way new operators and DSLs can be easily created.

## Abstract Classes
Same as in Java:
<pre><code class="scala hljs">// Scala
abstract class MyAbstractClass{
...
}</code></pre>

### Sealed Class Hierarchies

The `sealed` keyword in Scala is used mainly on base abstract classes or base traits and indicates that all subclasses must be declared in the same file. 