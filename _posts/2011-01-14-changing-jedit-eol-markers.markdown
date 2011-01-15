---
title: Changing the end-of-line marker in jEdit without recompiling
layout: post
---

jEdit is a great text editor. It has all the features you'd expect from a
modern text editor and, being written in Java, it's highly portable. One
feature that I make extensive use of is the "End of line markers" which allow
me to easily see if there are any extraneous spaces at the ends of lines.
But one thing that has always bothered me about jEdit's end-of-line (EOL)
markers is that they're displayed as periods. Mostly, displaying periods in
comments often leads to me thinking that I'm at the end of a sentence when I'm
not. Some programming languages also feature periods as a part of their
syntax, which can also lead to confusion.

Unfortunately, there's no setting in jEdit that allows me to change the
character used for the EOL marker, but that doesn't mean it's unchangeable!
Since jEdit is open-source, one option is to modify the source code and
recompile the application. However, even if I had the JDK installed, I don't
feel like waiting for jEdit to compile. The next best option (in my mind,
anyway) is to fire up the good ol' hex editor and change the bytecode. That's
exactly what I did.

![jEdit with modified EOL markers](../../../images/jedit.png "jEdit")

First, some background on JAR and class files. A JAR file is simply a ZIP file
with a `.jar` extension. A `class` file is a binary file with a particular
structure that includes, among other things, the constant pool and the
bytecode itself. The constant pool consists of an array of any literal data
that appeared in the source code. Each constant in the pool is prefixed with
one byte representing the type (1 for strings). For strings, the type is
followed by 2 bytes that represent the number of bytes in the string, and then
the string bytes encoded as (slightly modified) UTF-8.

Browsing through the jEdit JavaDocs, I noticed that
`org.gjt.sp.jedit.textarea.TextAreaPainter` has some methods pertaining to EOL
markers (namely `getEOLMarkersPainted()` and `getEOLMarkerColor()`), so that
seemed like the natural starting point. Looking at the source code for
`TextAreaPainter`, I found the line of code that draws the period (line 1207 in
jEdit 4.3.2). That line happens to be part of the `PaintText` private class.

Since the period is represented in the source as a string literal,
the compiler will add the literal to the constant pool in the class file. This
is particularly handy, since changing the period to another character only
requires changing the one literal in the constant pool.

First, I unzipped the jedit.jar file into a directory called jedit. I then
navigated to `org/gjt/sp/jedit/textarea` and opened the file
`TextAreaPainter$PaintText.class` with a hex editor. Since most hex editors
(including mine) display non-printing bytes as periods, it seems like 
locating the string in the constant pool was like finding a particular piece of
hay in a haystack. However, since the period we're looking for is represented
by the single byte `2E` in UTF-8, it was actually relatively straightforward.
To quickly find the constant pool, I searched for another string that was
easier to find: `" lines]"` from line 1196 in the source. From there, I found the
next `2E` byte. To verify that this was part of the constant pool, I inspected
the preceding 3 bytes: `01 00 01`, that is, this `2E` is a string (`01`) of one byte
(`00 01`). Replacing the period with another single-byte character is easy
enough: just overwrite the `2E` with another byte (for example, `7E` for `"~"`). But
the character I chose, the `¬` sign, is encoded with 2 bytes in UTF-8: `C2 AC`,
as shown by the character map:

![Character map](../../../images/charmap.png "Character map")

Normally, adding a byte to a binary file can be disastrous. Many types of files
contain information about the length of the file or offsets of particular
things in the header. Class files don't, however. All offsets in the class file
are specified as offsets from the end the constant pool. This is good news for
those of us who want to modify those constants!

But we can't just add the extra byte. We also have to change the length
information. That's easy enough: I just changed the `00 01` to `00 02`. Then, to
replace the period, I changed the `2E` to `C2`, and inserted the `AC` directly
afterwards. My final constant is `01 00 02 C2 AC`.

![GHex showing TextAreaPainter$PaintText.class](../../../images/ghex.png "GHex")

After saving the class file, I zipped the directory back into a JAR, opened
jEdit, and, lo and behold, my EOL markers had changed!

More detailed information about Java class files can be found in
[The Java Virtual Machine Specification, chapter 4](http://java.sun.com/docs/books/jvms/second_edition/html/ClassFile.doc.html "JVM Spec chapter 4").

My modified JAR file can be downloaded [here](../../../download/jedit.jar "jedit.jar").
