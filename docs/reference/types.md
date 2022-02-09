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
<verbatim>enum Gender {
  maleGender("Male"),
  femaleGender("Female")
}</verbatim>
You can use them as follows:
<verbatim>
entity User {
  gender -> Gender
}

define page somePage() {
  var u : User;
  input(u.gender) // shows a drop-down
  output(u.gender.name) // shows either Male or Female
}
</verbatim>
Or, in action code:
<verbatim>
function setMale(u : User) {
  u.gender := maleGender;
}
</verbatim>