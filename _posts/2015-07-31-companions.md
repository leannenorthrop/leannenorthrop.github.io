---
layout: post
title: Scala Case Classes &amp; Companions
tags: [scala, learning_scala]
blogpost: true
---
Simple data only classes like the Java Data Transfer Objects can be represented in Scala using a simplified class declaration using the keyword `case`. Each parameter to the primary constructor is treated as a read-only value (unless marked with the `var` keyword) with a private final field and accessor method generated for each by the compiler.

<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pet</span>(</span>name:<span class="hljs-type">String</span>,age:<span class="hljs-type">Int</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">myPet</span> =</span> <span class="hljs-type">Pet</span>(<span class="hljs-string">"Frodo"</span>, <span class="hljs-number">31</span>)
println(myPet.name + <span class="hljs-string">" "</span> + myPet.age)
</code></pre>

Just as with plain classes inheritance is possible as well:

<pre><code class="scala hljs"><span class="hljs-keyword">abstract</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">StoreProduct</span>(</span>name:<span class="hljs-type">String</span>)
<span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pear</span>(</span>) <span class="hljs-keyword">extends</span> <span class="hljs-type">StoreProduct</span>(<span class="hljs-string">"Pear"</span>)
<span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Apple</span>(</span>) <span class="hljs-keyword">extends</span> <span class="hljs-type">StoreProduct</span>(<span class="hljs-string">"Apple"</span>)
<span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Bannana</span>(</span>) <span class="hljs-keyword">extends</span> <span class="hljs-type">StoreProduct</span>(<span class="hljs-string">"Bannana"</span>)
</code></pre>

The `case` keyword causes the compiler to also generate useful methods such as toString, equals, hashCode and copy on the class (which can take one or more parameters by name to change values during the copy) and also a 'companion' singleton object e.g.

<pre><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Pear</span> {</span></pre>

Companion objects can be crafted by hand if it has the same name as the case class and resides in the same source code file.


## Apply
The companion object has an 'apply' method which has the same parameters as the primary constructor and returns a new instance of the case class (in other words it's a factory method):

<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pair</span>(</span>num1:<span class="hljs-type">Int</span>,num2:<span class="hljs-type">Int</span>)
<span class="hljs-comment">//This</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">pair1</span> =</span> <span class="hljs-type">Pair</span>.apply(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)
<span class="hljs-comment">// is the same as this</span>
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">pair2</span> =</span> <span class="hljs-type">Pair</span>(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)
</code></pre>

## Unapply
A similar 'unapply' method is generated which will convert the case class into a Tuple type which is useful for pattern matching.

<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pair</span>(</span>num1:<span class="hljs-type">Int</span>,num2:<span class="hljs-type">Int</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">pair1</span> =</span> <span class="hljs-type">Pair</span>(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">tuple</span> =</span> <span class="hljs-type">Pair</span>.unapply(pair1)
println(tuple) <span class="hljs-comment">// Prints: Some((1,3))</span>
</code></pre>

Unapply returns an Option since it can choose to return a None value rather than a tuple of the class values. If some values should be hidden a separate companion object with unapply should be created:

<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Pair</span>(</span>num1:<span class="hljs-type">Int</span>,num2:<span class="hljs-type">Int</span>)
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">PairHelper</span> {</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">unapply</span>(</span>pair:<span class="hljs-type">Pair</span>) : <span class="hljs-type">Option</span>[(<span class="hljs-type">Int</span>)] = {
        <span class="hljs-type">Option</span>((pair.num1))
    }
}
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">pair1</span> =</span> <span class="hljs-type">Pair</span>(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)
<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">tuple</span> :</span> <span class="hljs-type">Option</span>[(<span class="hljs-type">Int</span>)] = <span class="hljs-type">PairHelper</span>.unapply(pair1)
println(tuple) <span class="hljs-comment">// Prints: Some((1))</span>
</code></pre>

# Simulating a new keyword
Using the apply method is useful for creating a new keyword that operates over some block:

<pre><code class="scala hljs"><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">hello</span> {</span>
  <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">apply</span>(</span>doSomeWork: <span class="hljs-type">String</span> =&gt; <span class="hljs-type">String</span>) : <span class="hljs-type">String</span> = {
    doSomeWork(<span class="hljs-string">"hello"</span>)
  }
}

<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">result</span> =</span> hello { s =&gt;
  <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">length</span> =</span> s.length
  s<span class="hljs-string">"$s has $length characters"</span>.toUpperCase
}</code></pre>