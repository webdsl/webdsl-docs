# Types

This section lists all the built-in types available in WebDSL.

There are multiple types that are equivalent to String. These types can have different validation rules, functions, inputs, and outputs. Converting between these types can be done with casts, e.g.

    var : Secret := url("123") as Secret;

The String compatible types are:

* String
* Text
* WikiText
* Secret
* Email
* URL
* Patch

Similarly, the Date times are equivalent as well:

* Date
* Time
* DateTime

Enums
-----
Enumeration types are like `enum` in Java and other languages. You define them as follows:
```
enum Gender {
  maleGender("Male"),
  femaleGender("Female"),
  otherGender("Other"),
}
```
You can use them as follows:
```
entity User {
  gender : Gender
}

page somePage() {
  var u : User;
  input(u.gender) // shows a drop-down
  output(u.gender.name) // shows either Male or Female
}
```
Or, in action code:
```
function setMale(u : User) {
  u.gender := maleGender;
}
```

## String

Represents a string of characters. Example:

    var s : String := "Hello world";

The default value for String properties and variables is "".

---

### Functions

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

Creates a Patch from this String to the **new** String, see [Patch](#patch).

**diff(new : String):List<String>**

Creates a List<String> describing the differences between this String and the **new** String.


**trim(): String**

Returns this string, with leading and trailing whitespace omitted.

### List Functions

**concat():String**

Concatenates the strings in this list of strings.

**concat(separator:String):String**

Concatenates the strings in this list of strings, separated by **separator**.



## Int

Represents an integer number. Example:
    
    var i : Int := 3;

The default value for Int properties and variables is 0.

### Functions

**floatValue():Float**

Converts this value to a Float.

**toString():String**

Converts this value to a String.



## Float

Represents a floating point number. Example:

    var f : Float := 3.5;

The default value for Float properties and variables is 0f.

### Functions

**round():Int**

Rounds this value to the nearest Int value.

**floor():Int**

Returns the largest Int that is less than or equal to this value.

**ceil():Int**

Returns the smallest Int that is greater than or equal to this value.

**toString():String**

Converts this value to a String.


### Static Functions

**random():Float**

Produces a random Float between 0 and 1.



## Bool

Represents a truth value. Either <tt>true</tt> or <tt>false</tt>. Example:

    var b : Bool := true;

The default value for Bool properties and variables is false.

### Functions

**toString():String**

Converts this value to a String.



## List

Represents an ordered list of items of a certain type. Example:

    var l : List<Int> := [1, 2, 3, 4];

Sorted output of lists can be created using the `for` loop filter in [templates](https://webdsl.org/selectpage/Manual/Pages#ForLoopTemplate) or [actions](https://webdsl.org/selectpage/Manual/ActionCode#ForLoopAction):

    for(u:User in [u1,u2,u3] order by u.name desc){
      output(u)
    }

### Fields

**length**

Gives the number of items in the list.

### List Creation Expressions

**List<Entity>()**

Creates an empty list of type Entity.

**List<Entity>(..., Entity, ...)**

Creates a list of type Entity with the elements resulting from the comma separated argument expressions.

Example:

    var list := List<User>(User{},SubSubUser{},SubUser{})

**[Entity, ...]**

Creates a list of type Entity (type of first element) with the elements resulting from the comma separated expressions between the [ ].

Example:

    var list := [User{ name := "test" },SubUser{},uservar]

### Functions

**add(Entity)**

Adds the entity to this list.

**remove(Entity)**

Removes the first occurence of Entity in this list.

**clear()**

Removes all entities in this list.

**addAll(List/Set)**

Adds all entities of the List/Set to this list.

**set() : Set<Entity>**

Creates a Set containing the unique elements in this list.

**indexOf(Entity) : Int**

Returns the index of the first occurence of Entity in this list. Returns -1 if the Entity is not in this list.

**get(Int)**

Returns the element at location Int in this list.

**set(Int,Entity)**

Sets the list element at Int to Entity. If the Int is not within bounds, nothing is set, and a warning is shown in the log.

**insert(Int,Entity)**

Inserts the Entity at location Int in this list. If the Int is not within bounds, nothing is set, and a warning is shown in the log.

**removeAt(Int)**

Removes the element at location Int in this list. If the Int is not within bounds, nothing is set, and a warning is shown in the log.

**subList(from:Int,to:Int):List<Entity>**

Returns a portion of this list between the specified from, inclusive, and to, exclusive.



## Set

Represents an unordered collection of unique items of a certain type. Example

    var s : Set<Int> := {1, 2, 3, 4};

Sorted output of sets can be created using the `for` loop filter in [templates](https://webdsl.org/selectpage/Manual/Pages#ForLoopTemplate) or [actions](https://webdsl.org/selectpage/Manual/ActionCode#ForLoopAction). 

    for(u:User in [u1,u2,u3] order by u.name desc){
      output(u)
    }

### Fields

**length**

Gives the number of items in the set.

### Set Creation Expressions

**Set<Entity>()**

Creates an empty set of type Entity.

**Set<Entity>(..., Entity, ...)**

Creates a set of type Entity with the elements resulting from the comma separated argument expressions.

Example:

    var set := Set<Person>(Person{},SubSubPerson{},SubPerson{})

**{Entity, ...}**

Creates a set of type Entity (type of first element) with the elements resulting from the comma separated expressions.

Example:

    var set : Set<Person> := {Person{ name := "test" },personvar}

### Functions

**add(Entity)**

Adds the entity to this set.

**remove(Entity)**

Removes Entity in this set.

**clear()**

Removes all entities in this set.

**addAll(List/Set)**

Adds all entities of the List/Set to this set.

**list() : List<Entity>**

Creates a List containing the elements in this set.



## Secret

Represents a secret string (usually a password). The page input for a Secret is a masked textfield. The page output for a Secret is "\*\*\*\*\*\*\*\*".

Example:

    var pass : Secret := "123";

The default value for Secret properties and variables is "".

The Secret type is compatible with the String type, all the String functions can be used, and String literals can be assigned to Secret typed vars. A Secret can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(pass:Secret){
      test1(pass as String);    
    }

A String can also be cast to a Secret:

    assert(pass == "123" as Secret)

### Functions

**all [String](#string) functions**

Secret is compatible with String.

**check(input:Secret):Bool**

Checks the **input** Secret (not digested) against the digest version contained in this Secret.

Example:

    if (user.password.check(password)) {
      securityContext.principal := us;
      securityContext.loggedIn := true;
    }

**digest():Secret**

Generates a digest of the clear-text password contained in this Secret.

Example:
     
    var s : Secret := "123";
    s := s.digest();
    assert(s.check("123" as Secret));



## Email

Represents an e-mail address as a string. If you are interested in sending email from your application, have a look at the [Send Email](../send-email) page. The page input for an Email is a textfield with the following validation: 

    validate(/[a-zA-Z0-9_\-\.]+)@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.)|(([a-zA-Z0-9\-]+\.)+))([a-zA-Z]{2,4}|[0-9]{1,3})(\]?/.match(this), "Not a valid email address")

The page output for Email is the same as String output.

Example:

    var address : Email := "webdslorg@gmail.com";

The default value for Email properties and variables is "". Add the 'not empty' annotation (or a custom validation) to an Email type property in an entity to disallow the empty string.

The Email type is compatible with the String type, all the String functions can be used, and String literals can be assigned to Email typed vars. An Email can be cast to a String, this is necessary when calling functions or templates with String arguments. For example:

    function test1(s:String){}
    function test2(address:Email){
      test1(address as String);    
    }

A String can also be cast to an Email:

    assert(address == "123" as Email)

### Functions

**all [String](#string) functions**

Email is compatible with String.



## Text

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

### Functions

**all [String](#string) functions**

Text is compatible with String.



## WikiText

Represents a large string with [Markdown](http://daringfireball.net/projects/markdown/syntax) syntax support and internal page links.
Internal page links in WikiText can be created using `[[page(arg)|caption]]`.

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

### Functions

**all [String](#string) functions**

WikiText is compatible with String.



## Patch

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

### Functions

**all [String](#string) functions**

Patch is compatible with String.

**applyPatch(arg: String):String**

Applies this patch to the **arg** String.

Example:

    var s1 : Patch := "12345".makePatch("24");
    assert(s1.applyPatch("12345") == "24");




## DateTime

Represents both a date and a time. The page input for a DateTime is a textfield, the expected format is dd/MM/yyyy H:mm. The page output for a DateTime shows the DateTime formatted with dd/MM/yyyy H:mm. Use the format function to customize the output format.

The default value for DateTime properties and variables is null.

The DateTime type is compatible with the Time and Date types. A DateTime can be cast to these types.

### Date Creation Functions

**DateTime(String):DateTime**

Dates can be constructed using the <tt>Date</tt> constructor (expected format dd/MM/yyyy H:mm):

    var dt : DateTime := DateTime("22/06/1983 22:08");

**DateTime(String, String):DateTime**

    var dt : DateTime := DateTime("12:12 05-1994-06", "mm:H MM-yyyy-dd");

The second parameter represents the date/time formatting string.

**now():DateTime**

Creates a DateTime containing the current time and day.

### Functions

**format(formatstring:String):String**

Format this DateTime using the **formatstring**. See [Java SimpleDateFormat class documentation](http://www.j2ee.me/javase/6/docs/api/java/text/SimpleDateFormat.html) for formatstring syntax.

**before(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is before the **arg** date and time.

**after(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is after the **arg** date and time.

**addSeconds(amount:Int)**

Adds seconds, `amount` may be negative.

**addMinutes(amount:Int)**

Adds minutes, `amount` may be negative.

**addHours(amount:Int)**

Adds hours, `amount` may be negative.

**addDays(amount:Int)**

Adds days, `amount` may be negative.

**addMonths(amount:Int)**

Adds months, `amount` may be negative.

**addYears(amount:Int)**

Adds years, `amount` may be negative.

**getSecond():Int**

Gets the second.

**getMinute():Int**

Gets the minute.

**getHour():Int**

Gets the hour.

**getDay():Int**

Gets the day of the month.

**getDayOfYear():Int**

Gets the day of the year.

**getMonth():Int**

Gets the month.

**getYear():Int**

Gets the year.




## Date

Represents a date (not including a time). The page input for a Date is a textfield, the expected format is dd/MM/yyyy. The page output for a Date shows the Date formatted with dd/MM/yyyy. Use the format function to customize the output format.

The default value for Date properties and variables is null. Note that all Date types are DateTime at run-time.

The Date type is compatible with the DateTime and Time types. A Date can be cast to these types.

### Date Creation Functions

**Date(String):Date**

Dates can be constructed using the <tt>Date</tt> constructor (expected format dd/MM/yyyy):

    var d : Date := Date("04/09/2009");

**Date(String, String):Date**

The second parameter represents the date formatting string.

    var d1 : Date := Date("12-20-1990", "MM-dd-yyyy");

**today():Date**

Creates a Date containing the current day and time 00:00.

**age(Date):Int**

Gets the age from a date of birth.

### Functions

**format(formatstring:String):String**

Format this DateTime using the **formatstring**. See [Java SimpleDateFormat class documentation](http://download.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html) for formatstring syntax.

**before(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is before the **arg** date and time.

**after(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is after the **arg** date and time.

**addSeconds(amount:Int)**

Adds seconds, `amount` may be negative.

**addMinutes(amount:Int)**

Adds minutes, `amount` may be negative.

**addHours(amount:Int)**

Adds hours, `amount` may be negative.

**addDays(amount:Int)**

Adds days, `amount` may be negative.

**addMonths(amount:Int)**

Adds months, `amount` may be negative.

**addYears(amount:Int)**

Adds years, `amount` may be negative.



## Time

Represents a time (not including a date). The page input for a Time is a textfield, the expected format is H:mm. The page output for a Time shows the Time formatted with H:mm. Use the format function to customize the output format.

The default value for Time properties and variables is null. Note that all Date types are DateTime at run-time.

The Time type is compatible with the DateTime and Date types. A Time can be cast to these types.

### Time Creation Functions

**Time(String):Time**

Time can be constructed using the <tt>Time</tt> function (expected format H:mm):

    var t : Time := Time("22:08");

**Time(String, String):Time**

The second parameter represents the date formatting string.

    var t1 : Time := Time("59:08", "mm:H");

### Functions

**format(formatstring:String):String**

Format this DateTime using the **formatstring**. See [Java SimpleDateFormat class documentation](http://www.j2ee.me/javase/6/docs/api/java/text/SimpleDateFormat.html) for formatstring syntax.

**before(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is before the **arg** date and time.

**after(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is after the **arg** date and time.

**addSeconds(amount:Int)**

Adds seconds, `amount` may be negative.

**addMinutes(amount:Int)**

Adds minutes, `amount` may be negative.

**addHours(amount:Int)**

Adds hours, `amount` may be negative.

**addDays(amount:Int)**

Adds days, `amount` may be negative.

**addMonths(amount:Int)**

Adds months, `amount` may be negative.

**addYears(amount:Int)**

Adds years, `amount` may be negative.



## URL

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

### URL Creation Functions

**url(arg : String):URL**

Casts the **arg** String to a URL type, equivalent to 'arg as URL'. 

You can also use this in a navigate templatecall in order to provide an absolute URL instead of an internal link.

Example:

    navigate(url("https://webdsl.org")){ "powered by WebDSL" }  

### Functions

**all [String](#string) functions**

URL is compatible with String.



## File

Represents an (uploaded) file. The page input for a File is a file upload component. The page output is a file download link.

### Functions

**fileName():String**

Returns the name of this file.

**getContentAsString():String**

Returns the content of this file as String.

**download()**

The current action will result in a download of this file.




## Image

Represents an (uploaded) image. The page input for an Image is a file upload component. The page output shows the image.

### Functions

**fileName():String**

Returns the name of this image file.

**getContentAsString():String**

Returns the content of this file as String.

**download()**

The current action will result in a download of this image file.

**getWidth()**

Returns the calculated width of this image.

**getHeight()**

Returns the calculated height of this image.

**resize(maxWidth : Int, maxHeight : Int)**

Resizes the image to the set dimensions. Note that currently, images can only be downscaled.

**crop(x : Int, y : Int, width : Int, height : Int)**

Crops the image to the specified size and coordinates.

**clone() : Image**

Makes a copy of the image and returns it.
