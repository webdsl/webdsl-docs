# Action Code

This section describes the expressions and statements available in WebDSL.

## Expressions

**literals**  
A number of literals are supported:

* Strings: <tt>"This is a string"</tt>
* Ints: <tt>22</tt>
* Float: <tt>8.3</tt>
* Boolean: <tt>true</tt>/<tt>false</tt>
* List: <tt>[<expression>, <expression>, ...]</tt>
* Empty list: <tt>List<Int>()</tt>
* Set: <tt>{<expression>, <expression>, ...}</tt>
* Empty set: <tt>Set<Int>()</tt>
* Null: <tt>null</tt>

**operators**  
The following operators are supported:

* Addition (numeric types) and string concatenation: <tt>+</tt>
* Subtraction (numeric types): <tt>-</tt>
* Multiplication (numeric types): <tt>*</tt>
* Division (numeric types): <tt>/</tt>
* Modulus (integer type): <tt>%</tt>
* Casting (casts a variable as one of another type): <tt>as</tt> (example: <tt>8 as Float</tt>)

**binary operators**  

* Equality: <tt>==</tt>
* Inequality: <tt>!=</tt>
* Bigger than: <tt>></tt>
* Bigger than or equal to: <tt>>=</tt>
* Smaller than: <tt><</tt>
* Smaller than or equal to: <tt><=</tt>
* Instance of: <tt>is a</tt> (checks if a certain expression is of a certain runtime type)
* Contained in collection: <tt>in</tt> (checks if a certain expression is contained in a collection)
* and: <tt>&&</tt>
* or: <tt>||</tt>
* not: <tt>!</tt>

Example:

```
if(!(b is a String) && (b in [8, 5] || b + 3 = 7)) {
   // ...
}
```

**variables**  
Variables can be accessed by use of their identifiers and their properties using the . notation. Example: <tt>person.lastName</tt>

**indexed access**
List elements can be retrieved and assigned using index access syntax: 

<tt>var a := list[0]; 
list[2] := "test";</tt>



## Functions

Functions can be defined globally and as methods in entities:

    function sayHello(to : String) : String {
      return "Hello, " + to;
    }

    entity User {
      name : String

      function showName() : String {
        return sayHello(name + "!");
      }
    }

As of August 2016, entity functions without arguments can also be preceded with the `cache` keyword. This cache operates at the request level, i.e. it only calculates its value once per request. This is useful for cases where a more expensive function is repeatedly invoked (e.g. for access control).

```
entity SubForum {
  name : String
  managers : [User]
  ...

  cached function isManager() : String {
    return loggedIn() 
        && ( principal() in managers || parentForum.isManager() )
  }
}
```

## Variable Declaration

Variables can be defined globally, in pages (see [[page(PageVariables)]]), and in code blocks.

Syntax:

    var <identifier> : <Sort>;

This defines a variable within the current scope with name *identifier* and
type *Sort*.

Variable declarations can also have an expression that initializes the value:

    var <identifier> [: <Sort>] := expression;

The sort is optional in this case, if the Sort is not declared, the var will receive the type resulting from the expression (also known as local type inference).

Global variables always need an expression for initialization, they are added to the database once (when the first page is loaded, the database is checked to see whether all global vars have been created already). Global variables can be edited, but removing them can cause problems when there are explicit references to those variables.

Global variables can be further initialized using a global init{} block, e.g.

    var defaultUser := User{name:="default"}
    init{
      defaultUser.someInitializeFunction();
    }
    page root(){
      output(defaultUser.name)
    }

Global inits are also performed only once after database creation (if the dbmode is create-drop each new deploy will recreate the globals and execute inits, see [[page(ApplicationConfiguration)]]).

The ; is optional for global and page variable declarations.



## Assignment

The syntax of an assignment:

    <variable> := <value expression>;

Example:

    p.lastName := "Doe";

## Return

Syntax:

    return <expression>;

Example:
    
    function test():String{
      return p.lastName;
    }

In the context of a entity function this returns the expression as the
result of that function. In the context of an action or page init definition, it redirects the user to the page specified in the
expression.

Example:
    
    action done(){ return root(); }

## For-loop

Iterating a collection of entities or primitives can be done using a for loop. There are three types of for loop statements:

**For**
    
This type of for loop iterates the collection produced by expression e, which must contain elements of type t. The elements in the collection are accessible through identifier id.

The collection can be filtered:

        for(id:t in e){ stat* }
        for(id:t in e filter){ stat* }

**ForAll**

This for loop iterates all the entities in the database of type t. These can also be filtered. Note that it is more efficient to retrieve the objects using a filtering query and use the regular for loop above for iteration. 

        for(id:t){ stat* }
        for(id:t filter){ stat* }

**For Count**

This for loop iterates the numbers from e1 to e2-1.

        for(id:Int from e1 to e2){ stat* }

**For-loop Filter**

The filter part of a for loop can consist of four parts:

*Where*

        where e1 

e1 is a boolean expression which needs to evaluate to true for the element to be iterated.

*Order By*

        order by e2 asc/desc 

e2 is an expression that needs to produce a primitive type such as String or Int, which will be used to order the elements ascending or descending.

*Limit*

        limit e3

e3 is an Int expression which will limit the number of elements that get iterated.

*Offset*

        offset e4

e4 is an Int expression which will offset the starting element of the iteration.

Each of the four parts is optional, but they have to be specified in this order. The filtering is done in the application, so use queries instead of filters to optimize the WebDSL application.


## List Comprehension

List comprehensions are a combination of mapping, filtering and sorting. 

    [ e1 | id : t in e2 ] 

e2 produces a collection of elements with type t, e1 is an expression that allows transformation of the elements using identifier id.

Filters are also allowed:

    [ e1 | id : t in e2 filter ]

Example:

    [e.title | e : BlogEntry in b.entries 
               where e.created > date 
               order by e.created desc]

This expression returns all titles (e.title) from b.entries where the time created (e.created) is greater than a certain date, ordered by e.created in descending order. Both the where and order by clauses are optional. An ordering is either ascending (asc) or descending (desc).

**Conjunction**

    And [ e1 | id : t in e2 ] 

If e1 produces a boolean, the list comprehension can be preceded by "And" to create the conjunction of the elements produced by the list comprehension.

**Disjunction**

    Or [ e1 | id : t in e2 ] 

If e1 produces a boolean, the list comprehension can be preceded by "Or" to create the disjunction of the elements produced by the list comprehension.



## While Statement

Besides for loops, iteration can also be performed using the while statement.

        while(e){ stat* }

This will repeat stat* while e evaluates to true.
    


## Switch Statement

The case-statement has the following syntax:

    case(<expression>) {
      [case <expr-1> {
        <block executed if true>
      }] *
      [default {
        <block executed if no cases match>
      }]
    }</verbatim>

Any number of cases and optionally one default case can be specified.

Example:

    case(formatNumber) {
      1 {
        // format is one
      }
      2 {
        // format is two
      }
      default {
        // format is neither one nor two
      }
    }

## Regular Expressions / Regex

Examples in [codefinder](http://codefinder.org/doSearch/pq=%5Es.&op=AND&dff=_WebDSLNamespaceID_%2CrepoPath%2CfileExt%2C&type=Entry&fcnt=26%2C&dfp=120%2C200%2C120%2C&lim=10&sf=fileName%2Ccontent%2CcontentCase&ffld=fileExt%2C&sl=0&focc=SHOULD%2C&allowlcn=false&fvl=app%2C&///1)

## Render Template to String

The `rendertemplate` function can be used to render template contents to a String.

    rendertemplate(TemplateCall):String

Example:

    template test(a:Int){ output(a) "!" }
    function showContent(i:Int){
      log(rendertemplate(test(i)));
    } 

