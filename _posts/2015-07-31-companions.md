---
layout: post
title: Scala Case Classes &amp; Companions
tags: [scala, learning_scala]
---
Simple data only classes like the Java Data Transfer Objects can be represented in Scala using a simplified class declaration using the keyword `case`. Each parameter to the primary constructor is treated as a read-only value (unless marked with the `var` keyword) with a private final field and accessor method generated for each by the compiler.

<pre><code class="scala">case class Pet(name:String,age:Int)
val myPet = Pet("Frodo", 31)
println(myPet.name + " " + myPet.age)
</code></pre>

Just as with plain classes inheritance is possible as well:

<pre><code class="scala">abstract class StoreProduct(name:String)
case class Pear() extends StoreProduct("Pear")
case class Apple() extends StoreProduct("Apple")
case class Bannana() extends StoreProduct("Bannana")
</code></pre>

The `case` keyword causes the compiler to also generate useful methods such as toString, equals, hashCode and copy on the class (which can take one or more parameters by name to change values during the copy) and also a 'companion' singleton object e.g.

<pre><code class="scala">object Pear {
	...
}
</code></pre>

Companion objects can be crafted by hand if it has the same name as the case class and resides in the same source code file.


## Apply
The companion object has an 'apply' method which has the same parameters as the primary constructor and returns a new instance of the case class (in other words it's a factory method):

<pre><code class="scala">case class Pair(num1:Int,num2:Int)
//This
val pair1 = Pair.apply(1,3)
// is the same as this
val pair2 = Pair(1,3)
</code></pre>

## Unapply
A similar 'unapply' method is generated which will convert the case class into a Tuple type which is useful for pattern matching.

<pre><code class="scala">case class Pair(num1:Int,num2:Int)
val pair1 = Pair(1,3)
val tuple = Pair.unapply(pair1)
println(tuple) // Prints: Some((1,3))
</code></pre>

Unapply returns an Option since it can choose to return a None value rather than a tuple of the class values. If some values should be hidden a separate companion object with unapply should be created:

<pre><code class="scala">case class Pair(num1:Int,num2:Int)
object PairHelper {
    def unapply(pair:Pair) : Option[(Int)] = {
        Option((pair.num1))
    }
}
val pair1 = Pair(1,3)
val tuple : Option[(Int)] = PairHelper.unapply(pair1)
println(tuple) // Prints: Some((1))
</code></pre>

# Simulating a new keyword
Using the apply method is useful for creating a new keyword that operates over some block:

<pre><code class="scala">object hello {
	def apply(doSomeWork: String => String) : String = {
		doSomeWork("hello")
	}
}

val result = hello { s =>
	val length = s.length
	s"$s has $length characters".toUpperCase
}</code></pre>