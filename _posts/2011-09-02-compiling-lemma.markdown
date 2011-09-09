---
title: Compiling Lemma
layout: post
---

## {{page.title}}
#### 2 September 2011

I've been occasionally working on a [Lemma](http://github.com/baguette/lemma)
compiler for the last few weeks. I hadn't been planning on starting work on a
compiler so soon, but a few weeks ago I discovered a very serious bug with the
Lemma interpreter:
it ignores `nil` values altogether. I've been using Lua on and off for about
seven years and should've known better. The oversight was made very early on
in the course of developing the interpreter, and fixing it would require a
major amount of code-rewriting. However, since the interpreter was always
meant to be a throwaway, I decided to start from (almost) scratch and write a
compiler.

I decided to leave some room for more mistakes. The compiler I'm working on
is meant to be a one-off program. The idea is to get it working as quickly
as possible and see what kinds of compromises need to be made in order to
compile Lemma. It's a na&iuml;ve Lemma->Lua translator that does no
optimizations. As such, the code it generates isn't the best. The goal is to
be as much like the current Lemma language as possible and focus on Lua
interop.

When Lemma was first conceived, I had imagined an eventual compiler that
could generate Lua bytecode. After spending some time tinkering with the Lua
internals, it became clear that such an approach would not be feasible. While
the LVM executes bytecode, the format of the bytecode differs depending on
the platform. That means that LVM bytecode files are not portable: the
bytecode files generated on x86 are not quite the same as those generated on
amd64, for example. The bytecode files contain no information that would
allow LVMs on different platforms to decipher each other's bytecode.

Recent versions of LuaJIT, on the other hand, do support a portable bytecode.
This bytecode is portable between different platforms, but is only compatible
with LuaJIT and not with the official LVM.

The solution to this bytecode fiasco is, of course, to compile to Lua code.
From there, the bytecode generation can be handled by whatever Lua compiler
is available. This is less than ideal. Future versions of the compiler may
have support for multiple compilation targets and programmers could choose
either Lua code or LuaJIT bytecode.

The current source for the compiler is in `compiler.lma`. It's still a work
in progress, but it can compile most Lemma expressions. I've created a branch,
`0.2`, where I will continue work on the compiler. In `0.2`, the interpreter
in `eval` will be replaced with the compiler paired with `lua/loadstring` to
do a Lua compile and execute. I hope to get the language more
feature-complete at this time, and to begin rewriting the Lemma library
in Lemma itself (fixing bugs along the way). This is also a great opportunity
to start adding some documentation.

