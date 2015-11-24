---
layout: post
title: Pattern Power
tags: [scala, learning_scala]
blogpost: true
---
Instead of Java's switch statement Scala introduces a `match` expression which supports in-depth matching on an object's state or even simple expressions and returns the evaluated expression for the matching case. It looks fairly similar to switch blocks with `case` expressions but `break` is not required as there is no fall-through to the next case. The case block can contain case expressions operating over different types which straight-away gives a feeling of more expressive power to code than Java's switch.

<pre><code class="scala hljs"><span class="hljs-keyword">for</span> (item &lt;- <span class="hljs-type">List</span>(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3.0</span>, <span class="hljs-string">"a"</span>, <span class="hljs-string">"bc"</span>)) {
	<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">abc</span> =</span> item <span class="hljs-keyword">match</span> {
		<span class="hljs-keyword">case</span> <span class="hljs-number">1</span>			=&gt; <span class="hljs-string">"the number one."</span>
		<span class="hljs-keyword">case</span> i:<span class="hljs-type">Int</span>		=&gt; s<span class="hljs-string">"an integer, $i."</span>
		<span class="hljs-keyword">case</span> <span class="hljs-string">"a"</span>		=&gt; <span class="hljs-string">"an 'a' character"</span>
		<span class="hljs-keyword">case</span> d:<span class="hljs-type">Double</span> 	=&gt; s<span class="hljs-string">"a double, $d"</span>
		<span class="hljs-keyword">case</span> str:<span class="hljs-type">String</span>	=&gt; s<span class="hljs-string">"a string, $str"</span>
	}
	println(s<span class="hljs-string">"Found $abc"</span>)
}
<span class="hljs-comment">/* Prints:
Found the number one.
Found an integer, 4.
Found a double, 3.0
Found an 'a' character
Found a string, bc
*/</span></code></pre>

To catch unexpected cases use a 'catch-all' case expression:


<pre><code class="scala hljs"><span class="hljs-keyword">for</span> (item &lt;- <span class="hljs-type">List</span>(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3.0</span>, <span class="hljs-string">"a"</span>, <span class="hljs-string">"bc"</span>, 'b')) {
	<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">abc</span> =</span> item <span class="hljs-keyword">match</span> {
		<span class="hljs-keyword">case</span> <span class="hljs-number">1</span>			=&gt; <span class="hljs-string">"the number one."</span>
		<span class="hljs-keyword">case</span> i:<span class="hljs-type">Int</span>		=&gt; s<span class="hljs-string">"an integer, $i."</span>
		<span class="hljs-keyword">case</span> <span class="hljs-string">"a"</span>		=&gt; <span class="hljs-string">"an 'a' character"</span>
		<span class="hljs-keyword">case</span> d:<span class="hljs-type">Double</span> 	=&gt; s<span class="hljs-string">"a double, $d"</span>
		<span class="hljs-keyword">case</span> str:<span class="hljs-type">String</span>	=&gt; s<span class="hljs-string">"a string, $str"</span>
		<span class="hljs-keyword">case</span> unexpected	=&gt; s<span class="hljs-string">"unexpected value, $unexpected"</span>
	}
	println(s<span class="hljs-string">"Found $abc"</span>)
}
<span class="hljs-comment">/* Prints:
Found the number one.
Found an integer, 4.
Found a double, 3.0
Found an 'a' character
Found a string, bc
Found unexpected value, b
*/</span></code></pre>

Evaluation of case expressions are eager which is why the example places `case "a"` before `case str:String` and the default 'unexpected' is placed last. The _ placeholder character can be used in case expressions if the value is not required:

<pre><code class="scala hljs"><span class="hljs-keyword">for</span> (item &lt;- <span class="hljs-type">List</span>(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3.0</span>, <span class="hljs-string">"a"</span>, <span class="hljs-string">"bc"</span>, 'b')) {
	<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">abc</span> =</span> item <span class="hljs-keyword">match</span> {
		<span class="hljs-keyword">case</span> _:<span class="hljs-type">String</span>	=&gt; <span class="hljs-string">"a string"</span>
		<span class="hljs-keyword">case</span> unexpected	=&gt; s<span class="hljs-string">"unexpected value, $unexpected"</span>
	}
	println(s<span class="hljs-string">"Found $abc"</span>)
}
*/</code></pre><br/>


<div class="text-warning">
It's important to note that case variable names starting with an upper-case character are assumed by the compiler to be type name, whereas if a lower-case character it is assumed to be variable name:<br/><br/>


<pre><code class="scala hljs"><span class="hljs-keyword">case</span> <span class="hljs-type">Pair</span>(num1:<span class="hljs-type">Int</span>, num2:<span class="hljs-type">Int</span>)
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">PairHelper</span> {</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">unapply</span>(</span>pair:<span class="hljs-type">Pair</span>) : <span class="hljs-type">Option</span>[(<span class="hljs-type">Int</span>)] = {
        <span class="hljs-type">Option</span>((pair.num1))
    }
}

<span class="hljs-keyword">for</span> (item &lt;- <span class="hljs-type">List</span>(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3.0</span>, <span class="hljs-string">"a"</span>, <span class="hljs-string">"bc"</span>, 'b', <span class="hljs-type">Pair</span>(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>), <span class="hljs-type">Pair</span>(<span class="hljs-number">2</span>,<span class="hljs-number">3</span>))) {
	<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">abc</span> =</span> item <span class="hljs-keyword">match</span> {
	<span class="hljs-keyword">case</span> <span class="hljs-number">1</span>             =&gt; <span class="hljs-string">"the number one."</span>
	<span class="hljs-keyword">case</span> i:<span class="hljs-type">Int</span>         =&gt; s<span class="hljs-string">"an integer, $i."</span>
	<span class="hljs-keyword">case</span> <span class="hljs-string">"a"</span>           =&gt; <span class="hljs-string">"an 'a' character"</span>
	<span class="hljs-keyword">case</span> d:<span class="hljs-type">Double</span>      =&gt; s<span class="hljs-string">"a double, $d"</span>
	<span class="hljs-keyword">case</span> str:<span class="hljs-type">String</span>    =&gt; s<span class="hljs-string">"a string, $str"</span>
	<span class="hljs-keyword">case</span> <span class="hljs-type">PairHelper</span>(<span class="hljs-number">1</span>) =&gt; s<span class="hljs-string">"a pair, starting with 1"</span>
	<span class="hljs-keyword">case</span> <span class="hljs-type">Pair</span>(_,<span class="hljs-number">3</span>)     =&gt; s<span class="hljs-string">"a pair, ending with 3"</span>
	<span class="hljs-keyword">case</span> pair          =&gt; s<span class="hljs-string">"unexpected value, $pair"</span>
	}
	println(s<span class="hljs-string">"Found $abc"</span>)
}</code></pre>
</div>


## Backticks

Use backticks around a variable name to match on a previously defined variable value - without the backticks it means match anything. Also use backticks to refer to a previously defined variable.

## Or

Or is supported in a case expression with the pipe character e.g.:

<pre><code class="scala hljs"><span class="hljs-keyword">for</span> (item &lt;- <span class="hljs-type">List</span>(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3.0</span>, <span class="hljs-string">"a"</span>, <span class="hljs-string">"bc"</span>, 'b')) {
	<span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">abc</span> =</span> item <span class="hljs-keyword">match</span> {
		<span class="hljs-keyword">case</span> _:<span class="hljs-type">String</span> | _:<span class="hljs-type">Double</span>	=&gt; <span class="hljs-string">"a string or double found"</span>
		<span class="hljs-keyword">case</span> unexpected	=&gt; s<span class="hljs-string">"unexpected value, $unexpected"</span>
	}
	println(s<span class="hljs-string">"Found $abc"</span>)
}
</code></pre>

# Sequences
`Seq` is the parent of collections such as List which iterate over the collection in a fixed order. A sequence can be inspected using case expressions:

<pre><code class="scala hljs"><span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">seq</span> =</span> <span class="hljs-type">Seq</span>(<span class="hljs-string">"a"</span>, <span class="hljs-string">"b"</span>, <span class="hljs-string">"c"</span>)

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Helper</span> {</span>
	<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">toString</span>(</span>seq:<span class="hljs-type">Seq</span>[<span class="hljs-type">String</span>]) : <span class="hljs-type">String</span> = {
		seq <span class="hljs-keyword">match</span> {
			<span class="hljs-keyword">case</span> head +: tail =&gt; s<span class="hljs-string">"$head, "</span> + toString(tail)
			<span class="hljs-keyword">case</span> <span class="hljs-type">Nil</span> =&gt; <span class="hljs-string">"Nil"</span>
		}
	}
}

<span class="hljs-type">Helper</span>.toString(seq)</code></pre>
