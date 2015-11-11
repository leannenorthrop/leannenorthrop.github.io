---
layout: post
title: Pattern Power
tags: [scala, learning_scala]
blogpost: true
---
Instead of Java's switch statement Scala introduces a `match` expression which supports in-depth matching on an object's state or even simple expressions and returns the evaluated expression for the matching case. It looks fairly similar to switch blocks with `case` expressions but `break` is not required as there is no fall-through to the next case. The case block can contain case expressions operating over different types which straight-away gives a feeling of more expressive power to code than Java's switch.

<pre><code class="scala">for (item <- List(1, 4, 3.0, "a", "bc")) {
	val abc = item match {
		case 1			=> "the number one."
		case i:Int		=> s"an integer, $i."
		case "a"		=> "an 'a' character"
		case d:Double 	=> s"a double, $d"
		case str:String	=> s"a string, $str"
	}
	println(s"Found $abc")
}
/* Prints:
Found the number one.
Found an integer, 4.
Found a double, 3.0
Found an 'a' character
Found a string, bc
*/</code></pre>

To catch unexpected cases use a 'catch-all' case expression:


<pre><code class="scala">for (item <- List(1, 4, 3.0, "a", "bc", 'b')) {
	val abc = item match {
		case 1			=> "the number one."
		case i:Int		=> s"an integer, $i."
		case "a"		=> "an 'a' character"
		case d:Double 	=> s"a double, $d"
		case str:String	=> s"a string, $str"
		case unexpected	=> s"unexpected value, $unexpected"
	}
	println(s"Found $abc")
}
/* Prints:
Found the number one.
Found an integer, 4.
Found a double, 3.0
Found an 'a' character
Found a string, bc
Found unexpected value, b
*/</code></pre>

Evaluation of case expressions are eager which is why the example places `case "a"` before `case str:String` and the default 'unexpected' is placed last. The _ placeholder character can be used in case expressions if the value is not required:

<pre><code class="scala">for (item <- List(1, 4, 3.0, "a", "bc", 'b')) {
	val abc = item match {
		case _:String	=> "a string"
		case unexpected	=> s"unexpected value, $unexpected"
	}
	println(s"Found $abc")
}
*/</code></pre><br/>


<div class="text-warning">
It's important to note that case variable names starting with an upper-case character are assumed by the compiler to be type name, whereas if a lower-case character it is assumed to be variable name:<br/><br/>


<pre><code class="scala">case Pair(num1:Int, num2:Int)
object PairHelper {
    def unapply(pair:Pair) : Option[(Int)] = {
        Option((pair.num1))
    }
}

for (item <- List(1, 4, 3.0, "a", "bc", 'b', Pair(1,3), Pair(2,3))) {
	val abc = item match {
	case 1             => "the number one."
	case i:Int         => s"an integer, $i."
	case "a"           => "an 'a' character"
	case d:Double      => s"a double, $d"
	case str:String    => s"a string, $str"
	case PairHelper(1) => s"a pair, starting with 1"
	case Pair(_,3)     => s"a pair, ending with 3"
	case pair          => s"unexpected value, $pair"
	}
	println(s"Found $abc")
}</code></pre>
</div>


## Backticks

Use backticks around a variable name to match on a previously defined variable value - without the backticks it means match anything. Also use backticks to refer to a previously defined variable.

## Or

Or is supported in a case expression with the pipe character e.g.:

<pre><code class="scala">for (item <- List(1, 4, 3.0, "a", "bc", 'b')) {
	val abc = item match {
		case _:String | _:Double	=> "a string or double found"
		case unexpected	=> s"unexpected value, $unexpected"
	}
	println(s"Found $abc")
}
</code></pre>

# Sequences
`Seq` is the parent of collections such as List which iterate over the collection in a fixed order. A sequence can be inspected using case expressions:

<pre><code class="scala">val seq = Seq("a", "b", "c")

object Helper {
	def toString(seq:Seq[String]) : String = {
		seq match {
			case head +: tail => s"$head, " + toString(tail)
			case Nil => "Nil"
		}
	}
}

Helper.toString(seq)</code></pre>
