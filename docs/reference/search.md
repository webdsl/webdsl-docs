# Search

WebDSL supports simple search capabilities through [Lucene](http://lucene.apache.org). Entity properties can be marked as "searchable" to subsequently be indexed:
```
entity Message {
  subject : String (searchable)
  text    : Text   (searchable)
  sender  : ForumUser
}
```
The searchable can be applied to the following built-in WebDSL types: String, Text, WikiText, Int, Long, Float and date types. Properties of user defined entity types are currently not supported to be searchable (i.e. sender in the previous example).

If one or more properties of an entity are marked as searchable, a set of `searchEntity` functions are generated, in this case:
```
function searchMessage(query : String) : [Message]
function searchMessage(query : String, limit : Int) : [Message]
function searchMessage(query : String, limit : Int, offset : Int) : [Message]
```
Which can be used from anywhere. For instance on a search page:

```
page search(query : String) {
  var newQuery : String := query;
  action doSearch() {
    return search(newQuery);
  }

  title { "Search" }
  form {
    input(newQuery)
    submit("Search", doSearch())
  }
  for(m : Message in searchMessage(query, 50)) {
    output(m)
  }
}
```

The query syntax adheres to [Lucene's query syntax](http://lucene.apache.org/core/old_versioned_docs/versions/3_1_0/queryparsersyntax.html) as does the [scoring](http://lucene.apache.org/core/old_versioned_docs/versions/3_1_0/scoring.html).

All data to be indexed (properties marked as "searchable") and queries are analyzed using the default analyzer of Lucene. This means that punctuation and stop words (commonly used words like 'the', 'to', 'be') are stripped from text and text is transformed to tokens in lowercase.

## Index location

WebDSL stores its search index in the `/var/indexes/APPNAME` directory. This is currently not configurable. Make sure this directory is readable and writeable for the user that runs tomcat.

## Indexing

When the `searchable` annotations or search mappings are added/changed when data is already in the database, the search index has to be recreated. When your application has been deployed , go to the directory in which it was deployed, for instance /var/tomcat/webapps/yourapp. In this directory you will find a `webdsl-reindex` script (for *nix only) which will invoke ant reindex and fixes permissions of the index directory. 
By default, the reindex task completely reindexes all searchable entities. As of WebDSL version 1.2.9 it also accepts entity names as command line argument (separated by whitespace) to reindex a subset of entities.

__Reindex all entities__  
_*nix_: 
 
      # sudo sh webdsl-reindex

_Windows_  
as of 1.3.0:

      # ant reindex

before 1.3.0:

      # ant reindex -f build.reindex.xml 

__Reindex a subset of entities__  
_*nix_:  

      # sudo sh webdsl-reindex Entity1 Entity2 Etc

_Windows_:  
as of 1.3.0:

      # ant reindex -Dentities="Entity1 Entity2 Etc"
      
before 1.3.0:

      # ant reindex -f build.reindex.xml -Dentities="Entity1 Entity2 Etc"

Note: Don't start your application during reindexing, it will crash because it can't initialize the directory provider. So reindexing should be done before starting or when already running your application.

A demo of the search functionality can be seen on [Reposearch](http://www.codefinder.org).
