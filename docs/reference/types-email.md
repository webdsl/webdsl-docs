# Types - Email

Represents an e-mail address as a string. If you are interested in sending email from your application, have a look at the [SendEmail](https://webdsl.org/selectpage/Manual/SendEmail) page. The page input for an Email is a textfield with the following validation: 

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

---

Functions
----

**all [[page(String)|String]] functions**

Email is compatible with String.

---


