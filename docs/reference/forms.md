# Forms

The `form` element enables user input, and should include `submit` or `submitlink` elements to handle that user input. When pressing such a submit button/link, data binding will be performed for all inputs in the form.

    form {
      var name : String
      var pass : Secret
    
      label("Username:"){ input(name) }
      label("Password:"){ input(pass) }
      
      submit save() { "save" }
    }
    action save(){
      User{ 
        username := name 
        password := pass.digest()
      }.save();
    }

## Input

`input(<expression>)` creates an input form element. Can be applied directly to the properties of an entity (e.g., input(user.name)) or to page variables.

Input widgets are determined by the type of the property passed to the
input template call:

* String, Email, Int, Float, URL, Patch -> textfield
* Text, WikiText -> textarea
* Bool -> checkbox
* Date, DateTime, Time -> date picker
* List<Entity>, Set<Entity> -> multiselect (bug: List actually requires
a different type of input, to allow duplicates and control ordering)
* Entity -> select

For example, to get a checkbox, use:

    page root(){
     var x : Bool := false
     form{
       input(x)
       submit action{ log(x); } { "log result" }
     }
    }

or:

    entity TestEntity {
     x : Bool
    }
    template editTestEntity (e:TestEntity){
     form{
       input(e.x)
       submit action{ } { "update entity" }
     }
    }

## Page Variables

Page and template definitions can contain variables. This example displays "Dexter":

    page cat() {
      var c := Cat { name := "Dexter" }
      output(c.name)
    }

    entity Cat {
      name : String
    }

These variables are necessary when constructing a page that creates a new entity instance. The instance can be created in the variable and data binding can be used for the input page elements. The next example allows new cat entity instances to be created, and the default name in the input form is "Dexter":

    page newCat() {
      var c := Cat { name := "Dexter" }
      form{
        label("Cat's name:"){ input(c.name) }
        action("save",action{ c.save(); return showCat(c); })
      }
    }

It is possible to initialize such a page/template variable with arbitrary statments using an 'init' action:

    page newCat() {
      var c
      init {
        c := Cat{};
        c.name := "Dexter";
      }
      form{
        label("Cat's name:"){ input(c.name) }
        action("save",action{ c.save(); return showCat(c); })
      }
    }

Be aware that these type of variables (and the init blocks) are handled separately from the other elements. They do not adhere to template control flow constructs like 'if' and 'for'; they are extracted from the definition. However, you can express such functionality in the 'init' block. For example:

error:

    page bad() {
      if(someConditionFunction()){
        var c := Cat{}
      }
      else {
        var c := Cat{ name := "Dexter" }
      }
      output(c.name)
    }

ok:

    page good() {
      var c
      init{ 
        if(someConditionFunction()){
          c := Cat{}
        }
        else {
          c := Cat{ name := "Dexter" }
        }
      }
      output(c.name)
    }


## Select

`select(x, y)` can be used as input for an entity variable or for a collection of entities variable, where `x` is the variable or fieldaccess and `y` is the collection of options. It will create a dropdown box/select or a multi-select respectively. The `name` property of an entity is used to describe the entity in a select, see [[singlepage(nameproperty)| name property]].

`input(x)` for an entity reference property or a collection property is the same as `select`, with as options all entities of its type that are in the database.

Example:

    entity User {
      username : String (name)
      teammate : User
      group : Set<Group>
    }
    entity Group {
      groupname : String (name)
    }
    init{ //application init
      var u := User { username := "Alice" };
      u.save();
      u := User { username := "Bob"};
      u.save();
      var g := Group { groupname := "group 1" };
      g.save();
      g := Group { groupname := "group 2" };
      g.save();
    }
    page root(){
      form{
        table{
          for(u:User){
            output(u.username)
            input(u.teammate)
            input(u.group)
          }
        }
        submit("save",action{})
      }
    }

`input(u.teammate)` is a dropdown/select with options `null`, "Alice", "Bob". `input(u.group)` is a multi-select with options "group 1" and "group 2".

Example 2:

    page root(){
      var teammates := from User
      var groups := from Group
      form{
        table{
          for(u:User){
            output(u.username)
            select(u.teammate, teammates)
            select(u.group, groups)
          }
        }
        submit("save",action{})
      }
    }

Equivalent to the previous example, but using explicit selects instead.

Example 3:

    var u3 := User { username:="Dave" }
    var g3 := Group { groupname:="group 3" }

    page root(){
      var teammates := [u3]
      var groups := {g3}
      form{
        table{
          for(u:User){
            output(u.username)
            select(u.teammate, teammates)
            select(u.group, groups)
          }
        }
        submit("save",action{})
      }
    }

Options are restricted in this example, `null` and "Dave" for `select(u.teammate, teammates)` and only "group 3" for `select(u.group, groups)`

### Null

The `null` option for a select can be removed either by a `not null` annotation on the property:

    teammate : User (not null)

Or by setting `[not null]` on the `input` or `select` itself:

    input(u.teammate)[not null]
    select(u.teammate, teammates)[not null]

### Allowed

The possible options can also be determined using an annotation on the property:

    group : Set<Group> (allowed = {g3})

In this case just using `input(u.group)` will only show "group 3"

## Radio Buttons

Radio buttons can be used as an alternative to `select` for selecting an entity from a list of entities. The `name` property, or the property with `name` annotation, will be used as a label for the corresponding radio button.

    entity Person{
      name : String
      parent : Person
    }

    page editPerson(p:Person){
      radio(p.parent, getPersonList())
    } 

## Captcha

The `captcha` element creates a fully automatic [CAPTCHA](http://en.wikipedia.org/wiki/CAPTCHA) form element.

Example:

    page root(){
      var i : Int
      form{
        input(i)
        captcha()
        submit action{ Registration{ number := i }.save(); } {"save"}
      }
    }
