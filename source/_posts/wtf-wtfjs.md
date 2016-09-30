---
title: 'WTF, WTFJS?'
tags:
  - javascript wtf
id: 34
categories:
  - Hacking
date: 2010-02-18 10:23:13
---

My co-worker [Jason Ostrander](http://jasonostrander.posterous.com "Jason Ostrander") recently tipped me off about a new blog that's recently appeared, called [wtfjs.com](http://wtfjs.com). I'm all about geek humor, so I ran quickly to check it out.

My initial reaction was one of complete annoyance. Anyone who knows me knows that I am certainly no stick-in-the-mud, and I'm certainly willing to forgo some amount of technical accuracy in the name of a good laugh. But for some reason, the approach taken by this site rubs me the wrong way.

I think this is a great site with a lot of potential, but it needs a bit more love and care.
<!--more-->

My first major beef is that a large number of these WTFs  are really not specific to JavaScript at all, but rather to [IEEE 754 floating point numbers](http://en.wikipedia.org/wiki/IEEE_754-2008). Since this is the standard floating point representation used by computer hardware, there's nothing about it inherent to JavaScript. You can see this behavior with math functions in any programming language. It's arguable that the real WTF is that JavaScript has *only* floating point numbers as a numeric type, but that doesn't warrant regurgitating a bunch of odd behavior that's a result of floating point math.

My second concern is that a number of these WTFs are simply the result of someone with previous programming experience  jumping into JavaScript with reckless abandon, without reading any sort of decent documentation. Now, this is somewhat forgivable. For some reason, a greater portion of the tutorials in existence are simply flat-out bad. They encourage bad behavior, neglect to discuss important aspects of the language.

For example, I found it frustrating that a large number of the WTFs amount to understand the difference between type-coercing equality (==) and strict equality (===), or misunderstand about scoping rules.

Next, consider this example about [calling a function](http://wtfjs.com/post/385820317/thanks-to-http-twitter-com-tobeytailor-for):

<pre lang="c">
(x=[].reverse)() === window // true
</pre>

This might seem bizarre at first glance, but it has a really trivial explanation, and should be obvious to anyone with more than a surface knowledge of JavaScript. Since there's a technical discussion missing from the site, I'll mention it here. The dot syntax (object.function) for calling functions is a syntactic sugar for executing a piece of code such that any occurrences of the keyword **this** become references to the object before the dot. However, when you assign a function to a variable, you're simply associating a section of code with a name. The fact that the particular section of code is also the property of an object is not an inherent part of that function. This makes sense when you consider that you could define a function with global scope, and then assign it to an object property, later. So, when the function above is called, it's called in global scope. The reverse function returns **this**, but since **this** is no longer bound to an Array, but rather, the global object, that is what is returned. The proper way to write the expression above:

<pre lang="c">
(x=[].reverse).call([]) // []
</pre>

Finally, some of the expectations are just incorrect. For example, in one post, they express surprise at the result of the [expression (a + + b)](http://wtfjs.com/post/385887531/evaluted-as-foo-bar-which-converts) when a and b are strings. I think the real WTF is why anyone is ending up with code like that, anyway.

Another baffling [example](http://wtfjs.com/post/386185988/classic-null-is-not-an-object):
<pre lang="c">
typeof null // object
null === Object // false
</pre>
<span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;">This is the one that annoys me the most. Regardless of whether or not it's unexpected that null is treated as an object, why would ANY object be equal to its constructor (which is of type "function"?) This post is flat out misleading, it's the result of a natural language reading of a programming language construct.</span>

All said, [wtfjs](http://wtfjs.com) is a fun collection of gotchas. My initial reaction came because I took it as a site that was trying to undermine JavaScript as an actual language. But as the site progresses, it's looking more like a forum for sharing interesting gotchas of the language, even presented as logical faults or bugs in the language.

With the right love and care, I think this blog could manage to be a treasure trove of useful gems for those just starting to dabble with JavaScript. In particular, the following enhancements would go a long way:

*   Group or tag the posts somehow, to indicate the type of fault occurring. For example, a lot of the posts could be tagged as "IEEE754" or "equality" or "globals".
*   **Always** include a technical discussion of the fault, so that those reading the page have the opportunity to understand why they see the behavior they do, rather then having to treat it as some weird axiomatic case.