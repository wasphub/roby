The resultor pattern
===

In this short post belonging to [the series about extending the world of Linq](http://www.robychechi.it/roby/Tags/extending%20the%20world), I'll talk about an interesting way to leverage anonymous types through a pattern we use at work. We all know what they are, and we all know their most important limitation: once defined, an anonymous type cannot be passed to other functions or returned to the caller of the function which defined it (we are talking about language features, and we don't consider any technique based on reflection or other knowledge about the runtime internal structure of such types). It is quite common to write functions which return complex values, and if we want to make them reusable we need at some point to define some structure representing the result of the computation. We could use tuples, but they are not that good at communicating information, so we must surrender and define small DTOs just to represent those answers, just because anonymous type cannot cross the boundaries of a function. But is that completely true?

Let's suppose we need a function which parses string, and for each one we want to return a set of values. We could write something like this:

<pre class="brush:csharp">public static IEnumerable<DTO> Parse(
    this IEnumerable<string> source, 
    int someParam, string someOtherParam)
{
    return from item in source
           select new DTO
           {
               First  = item,
	       Second = item.Length,
	       Third  = new DateTime(2012, 1, 1),
	       Fourth = 42
           };
}

public class DTO
{
    public string   First   { get; set; }    
    public int      Second  { get; set; }    
    public DateTime Third   { get; set; }    
    public decimal  Fourth  { get; set; }    
}
</pre>

The body of the <span style="font-family:courier new,courier,monospace;">Parse</span> function is doing some stupid calculations, but that's not the point, what I want to show here is that for even for simple cases like this one we need to define a special class (<span style="font-family:courier new,courier,monospace;">DTO</span>) just for the sake of returning four values for each parsed string. It feels like a "waste" of power. We need to define some static type to return our answers. And even more disturbing, it often happens that the caller does not really need all the values that we are returning, but just some of them. 

But there is a way out. Type inference helps us to put a 'trick' in place and make anonymous types cross the function boundary. Let's look at our <span style="font-family:courier new,courier,monospace;">Parse</span> function: each time it parses a string it 'calculates' 4 values and it packs them in our <span style="font-family:courier new,courier,monospace;">DTO</span> to return them to the caller, but we could slightly change the point of view and say that the <span style="font-family:courier new,courier,monospace;">Parse</span> function calculates those values and 'passes' them to the caller. This subtle change makes us think that we could do what we always do in such cases: call a function.

Let's change our <span style="font-family:courier new,courier,monospace;">Parse</span> function signature like this:

<pre class="brush:csharp">public static IEnumerable<T> Parse<T>(
    this IEnumerable<string> source, 
    int someParam, string someOtherParam, 
    Func<string, int, DateTime, decimal, T> resultor)</pre>

In this new version we changed 2 things:

*   the return values is not an enumeration of <span style="font-family:courier new,courier,monospace;">DTO</span>s (or whatever well-defined type), but <span style="font-family:courier new,courier,monospace;">IEnumerable<T></span>
*   the same <span style="font-family:courier new,courier,monospace;">T</span> appears as part of the type of a new parameter we added to the already existing ones: this new parameter (a callback) is declared as a <span style="font-family:courier new,courier,monospace;">Func<...></span> with N+1 type parameters, where the first N types are the same types of the properties in the original <span style="font-family:courier new,courier,monospace;">DTO</span>, and the last type parameter is <span style="font-family:courier new,courier,monospace;">T</span>

This 'strange' signature is in fact very handy, and thanks to type inference we can modify our test call like this:

<pre class="brush:csharp">var parsed = from p in items.Parse(0, "wasp", 
                 (first, second, third, fourth) => new
                 {
                     First  = first,
                     Second = second,
                     Third  = third,
                     Fourth = fourth
                 })
             where p.First
                    .Equals("a", 
                            StringComparison.OrdinalIgnoreCase)
             select p;
</pre>

The lambda expression we pass as the last parameter in the call defines an anonymous type, which thanks to type inference becomes the <span style="font-family:courier new,courier,monospace;">T</span> of the generic <span style="font-family:courier new,courier,monospace;">Parse</span> function. The complete body of <span style="font-family:courier new,courier,monospace;">Parse</span> function could therefore be something like this:

<pre class="brush:csharp">public static IEnumerable<T> Parse<T>(
    this IEnumerable<string> source, 
    int someParam, string someOtherParam, 
    Func<string, int, DateTime, decimal, T> resultor)
{
    return from item in source 
	   select resultor 
           ( 
               item, 
               item.Length,
               new DateTime(2012, 1, 1), 
               42 
           ); 
}</pre>

It's very similar to the original version, but the <span style="font-family:courier new,courier,monospace;">new DTO()</span> part has been replaced to a function call to the supplied <span style="font-family:courier new,courier,monospace;">Func<...></span> delegate, which is called <span style="font-family:courier new,courier,monospace;">resultor</span>. So we now have moved the responsibility of creating a representation of our 'answer' outside of the <span style="font-family:courier new,courier,monospace;">Parse</span> function, which now just calls someone capable of building such a representation. Thanks to type inference we can define the builder on the fly through a lambda, and use an anonymous type to contain the representation. We don't need the <span style="font-family:courier new,courier,monospace;">DTO</span> class anymore, and one more advantage that we gain now is that we can easily handle cases where we are not interested in all the components of an answer. If for instance we just want to deal with the first and the fourth value, we can call <span style="font-family:courier new,courier,monospace;">Parse</span> like this:

<pre class="brush:csharp">var parsed = from p in items.Parse(0, "wasp", 
                 (p1, _, ___, p2) => new
                 {
                     This  = p1,
                     That = p2
                 })
             where p.This
                    .Equals("a",
                            StringComparison.OrdinalIgnoreCase)
             select p;
</pre>

I learned about this [at work](http://www.raboof.com), and there we name it the **resultor pattern**. So far I did not find anything around describing this technique explicily, Bill Wagner in one of his books talks about something similar but in a more basic way. It adds one more piece to our toolbox which helps us move our coding more 'into Linq'. It could be used in other contexts too, but it really shines inside Linq queries.
