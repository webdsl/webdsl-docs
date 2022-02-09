# Types - Secret

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

---

Functions
----

**all [[page(String)|String]] functions**

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

---


