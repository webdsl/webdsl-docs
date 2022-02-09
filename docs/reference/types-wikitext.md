# Types - WikiText

Represents a large string with [Markdown](http://daringfireball.net/projects/markdown/syntax) syntax support and internal page links.
Internal page links in WikiText can be created using [[ page(arg)|caption ]] (remove the spaces).

The page input for WikiText is a textarea. The page output for WikiText processes the Markdown and page links and produces html elements.

Example:

    var t : WikiText := "123";

The default value for WikiText properties and variables is "".

The WikiText type is compatible with the String type, all the String functions can be used, and String literals can be assigned to WikiText typed vars. A WikiText can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(t: WikiText){
      test1(t as String);    
    }

A String can also be cast to a WikiText:

    assert(t == "123" as WikiText)

---

Functions
----

**all [[page(String)|String]] functions**

WikiText is compatible with String.

