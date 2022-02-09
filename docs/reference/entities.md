# Entities

Data models in WebDSL are defined using *entity* definitions. An entity definition consists of the entity's name, possibly a super-entity from which it inherits, 0 or more properties and 0 or more entity functions:

    entity User {
      name     :: String (length = 25)
      email    :: Email
      password :: Secret
      homepage :: URL
      pages    -> Set<Page>
      function checkPassword(s : String) : Bool { 
        return password.check(s);
      }
      predicate sameUser(u:User){ this == u } 
    }

A property consists of 4 parts:

* a name
* a property kind, which can either be value (::), reference (->) or composite (<>)

The difference between reference and composite property kinds is that 
composite indicates that the referred entity is part of the one referring to it. The only effect this currently has is that composite cascades delete (deleting the entity will also delete the referred entity). 

* a property type, e.g. value types String, Int, Long, Text or reference/composite types which refer to other entities, such as Person, Set<Person>, and List<Person>. 


For a complete overview of the available types, see [[page(Types)|Types]].

* a set of annotations, for instance declaring inverse properties, lengths, validation.




An example data model for a blogging site:

    entity Author {
      name     :: String
      email    :: Email
      password :: Secret
      posts    -> Set<Post> (inverse=Post.author)
    }

    entity Post {
      author   -> Author
      title    :: String
      text     :: Text
      comments -> Set<Comment> (inverse=Comment.post)
    }

    entity Comment {
      post     -> Post
      author   :: String
      text     :: Text
    }


