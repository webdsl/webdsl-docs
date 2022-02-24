# Entities

Data models in WebDSL are defined using *entity* definitions. An entity definition consists of the entity's name, possibly a super-entity from which it inherits, 0 or more properties and 0 or more entity functions:

    entity User {
      name     : String (length = 25)
      email    : Email
      password : Secret
      homepage : URL
      pages    : {Page}
      function checkPassword( s: String ): Bool { 
        return password.check( s );
      }
      predicate sameUser( u: User ){ this == u } 
    }

A property consists of 3 parts:

* a name

* a property type, e.g. value types String, Int, Long, Text or reference/composite types which refer to other entities, such as Person, {Person} (set), and [Person] (list).

For a complete overview of the available types, see [Types](../types).

* a set of annotations, for instance declaring inverse properties, lengths, validation.




An example data model for a blogging site:

    entity Author {
      name     : String
      email    : Email
      password : Secret
      posts    : {Post} (inverse = author)
    }

    entity Post {
      author   : Author
      title    : String
      text     : Text
      comments : {Comment} (inverse = post)
    }

    entity Comment {
      post     : Post
      author   : String
      text     : Text
    }

## Instantiating Entity Objects

Instantiating new entity objects is done with the following expression:

    Entity{ [property := value]* }

The entity name followed by an optional list of property assignments between curly brackets.

Example:
    
    User{}
    User{ name := "Alice" }
    User{ name := "Bob" age := 34 }

Default initialization (what you would put into the constructor of an object in e.g. the Java programming language), can be added by extending the constructor function that is implicitly called.

Example:

    entity A : B{
      extend function A(){
        name := name +"A";
      }	
    }
  
    entity B{
      extend function B(){
        name := name +"B";
      }	
    }

    test constructors {
      var t := A{};
      assert(t.name == "BA");
    }

Creating an empty entity which doesn't call the constructor extensions can be done using createEmptyEntity, e.g. `createEmptyUser()`

## Name Property

The 'name' property is special, it is declared for each entity. By default it is a derived property that simply returns the id of the entity (which is also a special property declared for each entity, id:UUID is set automatically). 
The name can be customized by declaring a real name property:

    name : String

Or [derived name property](#derived-properties):

    name : String := firstname + lastname

Or by declaring a property as the name using an annotation:

    someproperty : String (name)

The name property is used in `input` and `select` template elements to refer to an entity. Example:

    application exampleapp
    init{
      var u := User{};
      u.save();
      u := User{};
      u.save();
      u := User{};
      u.save();
    }
    entity User{} 
    entity UserList{
      users : [User]
    }
    var globalList := UserList{}
    
    page root {
      for( u in globalList.users ){
        output(u.name) //there is always a name property
      }
      form{ 
        input( globalList.users ) //this will show three UUIDs as options
        submit action{ }{ "save" }
      }
    }
  
If the name is not a real property, you cannot create an input for it or assign to it.

Note that because 'name' is a derived property, by default you can't assign to it. To make 'name' mutable, you need to explicitly add it to the entity in one of the ways described above.

## Derived Properties

A derived property is a property of an entity (or [session entity](./session-entities/)) which is always equal to the result of an expression. The expression may reference other fields in the entity, but doesn't need to. Derived properties are always read-only. An example:

```
entity User {
  firstname: String
  lastname: String
  name : String := firstname + lastname
}
```

## List of field properties for entities

* `not null`. Makes sure a field isn't null
* `default=<value>`. Sets a default value. Note that this is different from [derived properties](#derived-properties)
* `allowed`. [See specific docs](#allowed-property-annotation) 

### Allowed Property Annotation

The `allowed` annotation for entity properties provides a way to restrict the choices the user has when the property is used in an input:

    entity Person{
      friends : {Person} (allowed = from Person as p where p != this)
    }
    var p1 := Person{}
    page root {
      form {
        input( p1.friends )
        submit action{ }{"save"}
      } 
    }

The allowed collection can be accessed through an entity function with name `allowed[PropertyName]`, e.g. `p1.allowedFriends()`

## Entity Inheritance

Entities can inherit properties and functions from other entities, like subclassing in Object-Oriented programming.

Example:

    entity Sub : Super {
      str : String
    }
    entity Super {
      i : Int
    }
    function test(){
      var e1 := Sub{ i := 1 str := "sdf" };
      var e2 := Super{ i := 1 };
    }

Subclass entities can be passed whenever an argument of one of its super types is expected.
  
Example:

    function test(){
      var e1 := Sub{ i := 1 str := "sdf" };
      test(e1);
    }
    function test(s:Super){
      log(s.i);
    }

Checking the dynamic type of an entity can be done using `isa` and casting is performed using `as`.

Example:

    function test(s:Super){
      if(s isa Sub){
        var su :Sub := s as Sub;
        log(su.str);
      }
    }
When specifically want to call a function from  the Superclass,  use the 'super' keyword.

Example: 

    entity Sub : Super {
      function foo() : Int {
        return super.foo();
      }
    }
    entity Super {
      function foo() : Int {
        return 42;
      }
    }

## Generated Properties for Entities

For defined entities, a number of properties are automatically generated.

### ID

    id : UUID

The id property is used in the database as key for the objects. The property is can only be read. 

### Version

    version : Int

The version property is a hibernate property which auto-increases for an object that is dirty when it is written to the database. 

### Created

    created : DateTime

The created property is a generated property which is set on the save of an object also with cascaded saves.

### Modified

    modified : DateTime

The modified property is a generated property which is automatically set on flush of an dirty object.

## Generated Functions for Entities

 For defined entities, a number of global functions are automatically generated. Replace Entity with the defined entity name below.

### Property with id annotation

If the Entity has an id annotation on a property, the following functions are generated (idtype is the type of the id property):

**getUniqueEntity**

    getUniqueEntity(id : idtype) : Entity
If the Entity with the given id already exists, it is returned. If it did not exist, it is created once and a flush to the database is performed (this will commit any changes made to the entities in memory, e.g. the changes from data binding of input fields), repeated calls to this function with the same argument will keep returning that created Entity.

**isUniqueEntity**
   
    isUniqueEntity(ent : Entity) : Bool
This function returns false when the value of the id property of ent is already taken. The function returns true when the id property is not taken, but will do so only once, subsequent calls with different entities but the same id will then return false (which makes this function suitable for processing a batch of entities in an action).

**isUniqueEntityId**

    isUniqueEntityId(id : idtype, ent : Entity) : Bool
This function returns false when the entity would not be unique when given the id argument. The function returns true when the entity would be unique, but will do so only once for a given id, checking a different entity with the same id will return false in the rest of the action handling.

    isUniqueEntityId(id : idtype) : Bool
This function returns false when the given id is not available for the Entity type. The function will return true only once, to cope with batch processing.

Note that these functions use one collection per entity to determine whether an id is available, so a call to isUniqueUserId(id) can influence the result of isUniqueUser(ent).

**findEntity**

    findEntity(id : idtype) : Entity
This function returns the Entity with the given id value, null if it does not exist.

### String property

For each String property in an Entity, a find function is generated (repace Property with the property name):

**findEntityByProperty**

    findEntityByProperty(val : String) : [Entity]
This function returns a list of all Entitys with the exact given Property value, an empty list if there are none.

**findEntityByPropertyLike**

    findEntityByPropertyLike(val : String) : [Entity]
This function returns a list of all Entitys with the given Property value as substring, an empty list if there are none.

### Entity Name

Every entity has a name, which is always a string. This name can be retrieved by the automatically generated getName() function.

The name of an entity is determined as follows:

1. If a property of the entity has the name annotation, the name of the entity equals this property. This property must be of type String.

2. If a property of the entity is called 'name' and is of type String, this property determines the entity name.

3. Otherwise, the id of the entity (converted to its string-value) is used. 

### Example

A typical scenario where these functions come in handy is a create/edit page for an entity. In the following example the isUniquePage function is used to verify that the new page has a unique identifier property:

    entity Page {
      identifier : String  (id, validate(isUniquePage(this), "Identifier is taken")
    }
    page createPage { 
      var p := Page{}
      form {
        label("Identifier"){input(p.identifier)}
        submit save() { "save" }
        action save(){
          p.save();
          message("New page created.");
          return home();
        }
      }  
    }

## Derive CRUD Pages

You can quickly generate basic pages for creating, reading, updating and deleting entities using `derive CRUD -entityname-`. It will create pages that allows creating and deleting such entities, and editing of all entities of this type in the database.

Example:

    application test

    entity User {
      username : String
    }

    derive CRUD User
 
    //application global var
    var u_1 := User{username:= "test"}
 
    page root {
      navigate(createUser()){ "create" } " "
      navigate(user(u_1)){ "view" } " "
      navigate(editUser(u_1)){ "edit" } " "
      navigate(manageUser()){ "manage" }
    }

As the navigates indicate, the pages that are created are:

view: 
    
    page entity(arg:Entity){...}

create: 

    page createEntity(){...}

edit: 
 
    page editEntity(arg:Entity){...}

manage (delete): 

    page manageEntity(){...}

These pages are particularly useful when you're just constructing the domain model, because the generated pages are usually too generic for a real application.
