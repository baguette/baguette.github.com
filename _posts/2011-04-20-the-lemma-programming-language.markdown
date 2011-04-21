---
title: The Lemma Programming Language
layout: post
---

## {{page.title}}
#### 20 April 2011

In [The Evolution of Lua](http://www.lua.org/doc/hopl.pdf), the Lua authors
write:

> Semantically, Lua has many similarities with Scheme, even though these
> similarities are not immediately clear because the two languages are
> syntactically very different. The influence of Scheme on Lua has gradually
> increased during Lua's evolution: initially, Scheme was just a language in the
> background, but later it became increasingly important as a source of
> inspiration, especially with the introduction of anonymous functions and full
> lexical scoping.

They go on to discuss some of the features shared by Lua and Scheme.

After reading this, I had an idea to write a toy Scheme interpreter that
exploited Lua as much as possible. As a toy, the interpreter wouldn't be
standard-compliant and would just be an exercise to explore the similarities
between the two languages.

Before I had written any code, I came across [Arc](http://arclanguage.org/).
There are many things I like about Arc. It's somewhat similar to Scheme, but it
was designed with practicality in mind. I like that Arc avoids superfluous
parentheses, reduces typing, and tries to provide constructs that make programs
more elegant. Browsing the Arc forum, I saw that writing new Arc implementations
seemed to be
[the](http://sourceforge.net/projects/jarc/files/ "Jarc")
[cool](http://github.com/conanite/rainbow/tree/master "Rainbow")
[thing](http://jonathan.tang.name/files/arclite/ "Arclite")
[to](https://github.com/akkartik/wart "wart")
[do](http://arclanguage.org/item?id=4715 "pseudoArc").
It was then that I decided to write an Arc implementation based on Lua, rather
than Scheme. I figured it'd be somewhat easier to make a more complete Arc
implementation than a complete Scheme one. This was the first step from
creating a toy to instead creating something useful.

But there were a few things about Arc that didn't really jive with me. First,
the language seems to have stagnated, prompting some supporters to
[fork](https://github.com/nex3/arc "Anarki") the official implementation.
Secondly, the slow rate of posts to the forum suggests a lack of interest in the
language. Furthermore, I don't particularly care for the language's emphasis on
mutability. There are a few other minor things, but I'm not going to get into
them.

I decided instead to start a new Lisp dialect, drawing influences from Scheme
and Arc. I wanted (and still want) this dialect to be practical with good Lua
integration. At this point, I recalled hearing about
[Clojure](http://clojure.org/ "Clojure"), a Lisp dialect designed with Java
integration in mind. I started learning Clojure to find some inspiration.
Clojure has a lot of really awesome features, and they have been making their
way slowly but surely into Lemma.

At this point, I don't plan for Lemma to be a port of Clojure to the LVM. For
one thing, the LVM has many differences with the JVM as a platform which call
for different design decisions. At this point, I haven't decided how similar
Lemma will be to Clojure. However, Lemma will likely be similar enough that
Clojure programmers shouldn't have difficulty picking it up (especially if
they're also familiar with Lua). I'll save Lemma's convergence with Clojure for
a future post `:)`

In short, Lemma will be a modern, practical Lisp dialect intended to target the
Lua platform. That is, Lemma is to Lua as Clojure is to Java.
