# Types - List

Represents an ordered list of items of a certain type. Example:

    var l : List<Int> := [1, 2, 3, 4];

Sorted output of lists can be created using the `for` loop filter in [templates](https://webdsl.org/selectpage/Manual/Pages#ForLoopTemplate) or [actions](https://webdsl.org/selectpage/Manual/ActionCode#ForLoopAction):

    for(u:User in [u1,u2,u3] order by u.name desc){
      output(u)
    }

---

Fields
----

**length**

Gives the number of items in the list.

---

List Creation Expressions
----

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

---

Functions
----

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
