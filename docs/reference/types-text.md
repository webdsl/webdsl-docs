# Types - Text

Represents a large string. The page input for a Text is a textarea. The page output for Text is the same as String output.

Example:

    var t : Text := "123";

The default value for Text properties and variables is "".

The Text type is compatible with the String type, all the String functions can be used, and String literals can be assigned to Text typed vars. A Text can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(t:Text){
      test1(t as String);    
    }

A String can also be cast to a Text:

    assert(t == "123" as Text)

---

Functions
----

**all [[page(String)|String]] functions**

Text is compatible with String.


