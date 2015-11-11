---
layout: post
title: Functions ~ The Basics
tags: [scala, learning_scala, functions]
blogpost: true
---
Scala has support for functions as first-class values in that functions can be assigned to variables, and passed and returned from other functions.

<pre><code style="scala">//Scala
// Function Literal
val myFunction = (s:String) => s.toUpperCase()

// Call myFunction and print result
val result = myFunction("Leanne")
println(result) // Prints: LEANNE

// Pass myFunction to another function and print result
val cart = List("Pear", "Apple", "Bannana")
val result2 = cart.map(myFunction)
println(result2) // Prints: List(PEAR, APPLE, BANNANA)

// Functional Literal
val cart = List("Pear", "Apple", "Bannana")
val result3 = cart.map((s : String) => s.toUpperCase())
println(result3) // Prints: List(PEAR, APPLE, BANNANA)

// Simpler Functional Literal
val cart = List("Pear", "Apple", "Bannana")
val result3 = cart.map(_.toUpperCase()) // Use the _ character to refer to the first parameter. 
println(result3) // Prints: List(PEAR, APPLE, BANNANA)
</code></pre>

When a function like `map` takes a function as a parameter it is called a higher-order function. When the return type is Unit (Scala equivalent to Java's void) the function is not pure but instead only causes side-effects.

## Nested Functions

<pre><code class="scala">object Math {
	def !(i:Int):Long = {
		def factorial(i:Int, acc:Int):Long = {
			if (i <= 1) acc
			else factorial(i-1, i * acc)
		}
		factorial(i,1)
	}
}
println(Math ! 4) // Prints: 24
</code></pre>

### Tail Recursion 
To ensure that Scala compiler implements tail-call optimisation use the `@tailrec` annotation which will cause the compiler to print a warning if there is an error. This will prevent stack overflow.

<pre><code class="scala">object Math {
	def !(i:Int):Long = {
		@tailrec
		def factorial(i:Int, acc:Int):Long = {
			if (i <= 1) acc
			else factorial(i-1, i * acc)
		}
		factorial(i,1)
	}
}
println(Math ! 4) // Prints: 24
</code></pre>

## Partial Functions
Not to be confused with partially applied functions, partial functions are those which implement functionality for only a specific sub-group of the input. Only `case` clauses are allowed and must be contained within a pair of curly braces. In Scala it must take a single parameter and can take a literal form:

<pre><code class="scala">val myPartialFunction : PartialFunction[String,String]= {
    case "hello" => "found hello"
    case "goodbye" => "found goodbye"
}
println(myPartialFunction("hello")) // Prints: found hello
println(myPartialFunction("goodbye")) // Prints: found good-by
println(myPartialFunction("thinking")) /* Prints:
scala.MatchError: thinking (of class java.lang.String)
  at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:253)
  at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:251)
  ... elided*/
</code></pre>

To prevent the MatchError a `isDefinedAt` method can be be used which return true if a MatchError will not be thrown.

<pre><code class="scala">val aString : PartialFunction[Any,String]= {
    case "hello" => "found string hello"
    case s:String => "found string " + s
}
val anInt : PartialFunction[Any,String]= {
    case i:Int => "found int " + i
}
val anything : PartialFunction[Any,String]= {
    case unexpected => "found unknown anything " + unexpected
}
val combined = aString orElse anInt orElse anything

println(combined("hello")) // Prints: found hello
println(combined("goodbye")) // Prints: found good-by
println(combined(1)) // Prints: found int 1
println(combined(5.0)) // Prints: found unknown anything 5.0
</code></pre>