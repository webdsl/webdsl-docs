# Types - URL

Represents a hyperlink. The page input for a URL is the same as for String. The page output for URL is a hyperlink.

Example:

    var u : URL := "https://webdsl.org";

The default value for URL properties and variables is "".

The URL type is compatible with the String type, all the String functions can be used, and String literals can be assigned to URL typed vars. A URL can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(t: URL){
      test1(t as String);    
    }

A String can also be cast to a URL:

    assert(t == "https://webdsl.org" as URL);

---

URL Creation Functions
----

**url(arg : String):URL**

Casts the **arg** String to a URL type, equivalent to 'arg as URL'. 

You can also use this in a navigate templatecall in order to provide an absolute URL instead of an internal link.

Example:

    navigate(url("https://webdsl.org")){ "powered by WebDSL" }  

Functions
----

**all [[page(String)|String]] functions**

URL is compatible with String.


