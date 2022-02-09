# Types - String

Represents a string of characters. Example:

    var s : String := "Hello world";

The default value for String properties and variables is "".

---

Functions
----

**contains(s: String):Bool**

Tests whether **s** is a substring of this string.

**length():Int**

Returns the length of this string.

**parseInt():Int**

Returns the Int value in this string. If this string does not contain a valid Int value, this function returns null.

**parseUUID():UUID**
    
Returns the UUID value in this string. If this string does not contain a valid UUID value, this function returns null.

**toUpperCase():String**

Returns this string in uppercase.

**toLowerCase():String**

Returns this string in lowercase.

**split():List<String>**

Returns the characters in this string as separate strings in a list.

**split(separator:String):List<String>**

Returns a list of strings produced by splitting this string around matches of **separator**.

**makePatch(new : String):Patch**

Creates a Patch from this String to the **new** String, see [[page(Patch)|Patch type]].

**diff(new : String):List<String>**

Creates a List<String> describing the differences between this String and the **new** String.


**trim(): String**

Returns this string, with leading and trailing whitespace omitted.
---

List Functions
----

**concat():String**

Concatenates the strings in this list of strings.

**concat(separator:String):String**

Concatenates the strings in this list of strings, separated by **separator**.
