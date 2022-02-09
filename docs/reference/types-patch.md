# Types - Patch

Represents a patch. The page input for a Patch is the same as for Text. The page output for Patch is the same as for Text.

Example:

    var p : Patch := "12345".makePatch("24");

The default value for Patch properties and variables is "".

The Patch type is compatible with the String type, all the String functions can be used, and String literals can be assigned to Patch typed vars. A Patch can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(p:Patch){
      test1(p as String);    
    }

A String can also be cast to a Patch:

    assert(p == "123" as Patch);

---

Functions
----

**all [[page(String)|String]] functions**

Patch is compatible with String.

**applyPatch(arg: String):String**

Applies this patch to the **arg** String.

Example:

    var s1 : Patch := "12345".makePatch("24");
    assert(s1.applyPatch("12345") == "24");

