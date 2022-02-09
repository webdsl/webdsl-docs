# Types - Set

Represents an unordered collection of unique items of a certain type. Example

    var s : Set<Int> := {1, 2, 3, 4};

Sorted output of sets can be created using the `for` loop filter in [templates](https://webdsl.org/selectpage/Manual/Pages#ForLoopTemplate) or [actions](https://webdsl.org/selectpage/Manual/ActionCode#ForLoopAction). 

    for(u:User in [u1,u2,u3] order by u.name desc){
      output(u)
    }

---

Fields
----

**length**

Gives the number of items in the set.

---

Set Creation Expressions
----

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

---

Functions
----


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
