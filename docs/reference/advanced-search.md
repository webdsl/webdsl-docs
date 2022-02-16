# Advanced Search

_note: Some syntax changes and additional features are expected for WebDSL [1.3.0](http://yellowgrass.org/tag/WebDSL/1.3.0). The search language will become more structured. This manual will be updated/completed upon 1.3.0 release_

WebDSL offers full text search engine capabilities based on Apache [Lucene](http://lucene.apache.org) and [Hibernate Search](http://www.hibernate.org/subprojects/search).
Current implementation supports:

 * Set up which entity properties are searchable
 * Full text search on (a subset of) searchable entity properties
 * Range queries (numeric and date properties)
 * Boolean queries 
 * Faceted search (both discrete values and ranges) 
 * Index and query time boosting
 * Customized preprocessing of searchable properties/queries using [SORL analyzer](http://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters) building blocks (tokenizers, character and token filters):
   - Common analyzers are predefined
   - Use custom stop words that are ignored at indexing/querying
   - Get more relevant results by
      * Synonym analyzer (ipad i-pad i pad all match the same)
      * Stem words to their root word (performing -> perform)
      * Phonetic search (words that sound similar are matched)
   -  [Many more](http://lucene.apache.org/solr/api-3_6_1/org/apache/solr/analysis/package-summary.html)
 * Filter search results by property value or faceted search
 * Sort search results
 * Pagination of search results
 * Result highlighting
 * Spell checking
 * Auto completion
 * Create search name spaces based on property value

## Making your data searchable
Using search in WebDSL starts by marking which entities need to be searchable. If one property is marked searchable, the entity can be searched. For each entity property one or more search fields can be specified. There are 2 ways to specify these: using search mappings or using searchable annotations. For simple search functionality, searchable annotations will suffice, but for cleaner code we recommend using search mappings.

## Using search mappings (recommended)
A search mapping starts with the name of the property to be indexed, optionally followed by _mapping specifications_:

##### as <code>name</code>
Override the default search field <code>name</code>. Default: property name

##### using <code>analyzer</code>
Indexed using analyzer <code>analyzer</code> instead of the default analyzer.

##### boosted to <code>Float</code>|^<code>Float</code>
Search field is boosted to <code>Float</code> at index time (default 1.0).

##### (spellcheck)|(autocomplete)|(spellcheck,autocomplete)
Indicate that this search field can be used for spell checking/autocompletion.

##### for subclass <code>entity</code>
In case marking an reference/composite property as searchable, you might want to make only a specific subclass of the property type searchable.

##### depth <code>Int</code>|with depth <code>Int</code>
In case marking an reference/composite property as searchable, you can specify the depth of the 'embedded' path, 1 is the default.

##### + <code>mapping specification</code>
Prefix a <code>mapping specification</code> with the plus sign if you want this search field to be used by default at query time. <u>If no default search field is specified, all search fields are used by default</u>.


Search mappings belong to an entity and can be placed inside an entity declaration, or somewhere else by adding the entity name. Names of the search fields are scoped to entities, so different entities may share the same names for search fields.

```
//Embedded search mapping
entity Message {
  subject  : String
  text     : Text
  category : String
  sender   : User

  search mapping {
    +subject
    +text using snowballporter as textSnowBall
    text
    category
    +sender for subclass ForumUser
  }
}
```

```
//External search mapping
entity ForumUser : User {
  forumName : String
  forumPwd  : Secret
  messages  : Set<Message> (inverse=Message.sender)
}
...
search mapping ForumUser {
  forumName using none
}
```

## Using annotations

Search fields can also be specified using property annotations:
```
//Using searchable annotations
entity Message {
  subject  : String (searchable)
  text     : Text (searchable, searchable(name=textSnowBall, analyzer=snowballporter)
  category : String (searchable)
  sender   : ForumUser (searchable())
}
```

The above code marks the entity `Message` searchable, and it has 3 search fields: `subject`, `text` using the default analyzer, and `textSnowball`, which uses the snowball porter analyzer. Searchable annotations have no restriction w.r.t. search mappings, and both can be used interchangeably (not recommended since it's less transparent). The following table shows the annotation equivalent of specifications in search mappings.

<table>
    <tr>
        <td><b>search mapping</b></td>
        <td><-></td>
        <td><b>searchable annotation</b></td>
    </tr>
    <tr>
        <td><sub>subject</sub></td>
        <td><-></td>
        <td><sub>searchable<br/>searchable()</sub></td>
    </tr>
    <tr>
        <td><sub>subject as sbj</sub></td>
        <td><-></td>
        <td><sub>searchable(name = sbj)</sub></td>
    </tr>
    <tr>
        <td><sub>subject using defaultNoStop</sub></td>
        <td><-></td>
        <td><sub>searchable(analyzer = defaultNoStop)</sub></td>
    </tr>
    <tr>
        <td><sub>subject^2.0</sub></td>
        <td><-></td>
        <td><sub>searchable()^2.0</sub></td>
    </tr>
    <tr>
        <td><sub>subject boosted to 2.0</sub></td>
        <td><-></td>
        <td><sub>searchable(boost = 2.0)</sub></td>
    <tr>
        <td><sub>subject as sbjTriGram using trigram boosted to 0.5</sub></td>
        <td><-></td>
        <td><sub>searchable(analyzer = trigram, name = sbjTriGram)^0.5</sub></td>
    </tr>
    <tr>
        <td><sub>subject as sbjUntokenized using none</sub></td>
        <td><-></td>
        <td><sub>searchable(analyzer = none, name = sbjUntokenized)</sub></td>
    </tr>
    <tr>
        <td><sub>message as sbjAC using kwAnalyzer (autocomplete)</sub></td>
        <td><-></td>
        <td><sub>searchable(analyzer = kwAnalyzer, name = sbjAC, autocomplete)</sub></td>
    </tr>
    <tr>
        <td><sub>user as forumuser for subclass ForumUser</sub></td>
        <td><-></td>
        <td><sub>searchable(name = forumuser, subclass = ForumUser)</sub></td>
    <tr>
    <tr>
        <td><sub>user with depth 2</sub></td>
        <td><-></td>
        <td><sub>searchable(depth=2)</sub></td>
    <tr>
        <td><sub>+ text as txt</sub></td>
        <td><-></td>
        <td><sub>searchable(name = txt, default)</sub></td>
    </tr>
    </tr>
</table>

## Which properties can be made searchable ?
Properties of any type can be made searchable, although there are some notes to make.
### Reference and composite properties ##
These properties don't contain any text or value by themselves, but hold references to other entities. Therefore, the properties themselves cannot be indexed, but the searchable properties of the referred entity/entities will be indexed in the scope of the current entity. For example if you want to be able to search for `Message` entities by the name of the sender (in the above example), the property `forumName` of `ForumUser` needs to be indexed in the scope of `Message`. This can be done by marking the `sender` property as searchable. All search fields from `ForumUser` will then be available for `Message`, and searchfields are prefixed with '`propertyName.`' by default (or different name if specified using `as` in search mappings). The search field from the example becomes :  `sender.forumName`. 

Note: Searchable reference/composite properties need to be part of an inverse relation to keep the index of the owning entity updated with changes in its reference entity/entities.
The mapping options available for reference properties are restricted to `name` and `subclass`.

### Numeric properties (Float,Int,Date,DateTime,Time)
In case no analyzer is specified for a numeric property search field, it will be indexed as numeric fields, which is a special type of field in Lucene. It enables efficient range queries and sorting on this field.

### Derived properties
Derived properties are currently only indexed when the entity owning this property is saved/changed.

## How to analyze your data/queries
By default, textual properties will use the default analyzer from Lucene, which is optimized for the English language. In the specification of a search field (in search mapping or searchable annotation), a different analyzer can be assigned to it like is done for the `textSnowBall` search field. A custom analyzers can be declared, each containing:

 * zero or more character filters
 * one tokenizer
 * zero or more token filters

The range of tokenizers and filters that are supported can be found [here](http://lucene.apache.org/solr/api-3_6_1/org/apache/solr/analysis/package-summary.html) and [here](http://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters) (with more information about specific analyzers). You don't need to use the factory keyword at the end. Useful analyzers definitions are already included in a new WebDSL project under ./search/[searchconfiguration.app](https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/src/org/webdsl/dsl/project/new_project/search/searchconfiguration.app). The default analyzer can be overwritten by adding the `default` keyword before `analyzer`.
More advanced analysis may require different behavior at search and query time. Using the `index { ... }` and `query { ... }` block, the analyzers may be specified different for indexing and query time (see the [synonym analyzer](https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/src/org/webdsl/dsl/project/new_project/search/searchconfiguration.app)).

## Searching the data!
For each indexed entity, search functions and a searcher class are automatically generated. For simple searches, the generated functions will suffice. For more advanced searches, the magic is in the generated entity searcher(s).
### Search data using generated search functions
For the example entity `Message`, the following search functions are generated.
```
function searchMessage(query : String) : List<Message>
function searchMessage(query : String, limit : Int)
                             : List<Message>
function searchMessage(query : String, limit : Int,
                       offset : Int) : List<Message>
```
The limit and offset parameters can be used for paginated results. It only loads at most the `limit` number of results from the database (for efficiency/faster pageloading). These functions use the default search fields when searching, and the specified analyzers are applied for each search field.


### Search data using WebDSL search language for full text search

More features are available when using WebDSL's search language designed to perform search operations. The language let you interact with the generated Searcher object for the targeted entity. A reference to (or initialization of) a searcher is followed by one or more constructs in which search criteria can be declared.

```
//matches Messages with "tablet", but without "ipad"
var msgSearcher := search Message matching +"tablet", -"ipad";

//enable faceting on an existing searcher
msgSearcher := ~msgSearcher with facets sender.forumName(20), category(10)
```

List of search language constructs:

## Retrieving search results
```
var searcher := search Book matching author: "dahl";
var results := searcher.results(); //returns List<Book>;
```
Calling .results() on a searcher returns the search results.
Calling .count() on a searcher returns the total number of results.

## Simple and boolean queries: 'matching { [{field ,}:] {qExp ,} ,}'
```
searcher := search Entity matching title: "user interface";
searcher := search Entity matching title, description: userQuery; 
searcher := search Entity matching "user interface";
searcher := search Entity matching title: +userQuery, -"case study"; 
searcher := search Entity matching ranking:4 to 5, title:-"language"; 
```
Declares a searcher that matches a simple or boolean query. Fields are optional: if the query expression is not preceded by a field constraint, the default search fields are used (i.e. all search fields if no default fields are defined, see ...). qExp can be any String compatible WebDSL expression or a range expression optionally prefixed with a boolean operator (+ for must, - for mustnot, nothing for should).

## Range queries
```
searcher := search Entity matching rating: {1 to 3}
searcher := search Entity matching rating: [startDate to endDate]
searcher := search Entity matching rating: -[* to sinceDate]
```
Range expressions are in the form _[minExp to maxExp]_ (including min and max value) or _{minExp to maxExp}_ (exludes min and max, where both expressions can be any expression of a simple WebDSL builtin type. An open range is specified with an asterisk : _[* to "A"}_ for example.

## Pagination
```
var searcher := search Book matching author: "dahl" start 20 limit 10
```
With the _start_ and _limit_ keywords, you can control which results to be retrieved.

## Configuration options: '[ {option* ,} ]'
```
searcher := search Entity on title: q [no lucene, strict matching];
```
Declare the searcher's options. Available options are:

*   _lucene_: allow [lucene query syntax](http://lucene.apache.org/java/3_1_0/queryparsersyntax.html)
*   _no lucene_: disallow lucene query syntax
*   _strict matching_: all terms must match by default
*   _loose matching_: at least one term should match by default


## Filtering: 'with filter(s) {filterconstraint* ,}'
```
searcher := search Entity matching title: "graph" 
                          with filter hidden:false;
```
Specify a filter constraint. A filter constraint is a field-value expression.
Be aware that when using a filter, a bitset is constructed and cached to accelerate future queries using the same filter. Filters are not considered in result ranking. Thus, only use field-value filters if you expect the same filtering to occur frequently.

## Enabling facets: 'with facet(s) field1(e1), field2(e2)'
Example:
```
searcher := search Entity matching title: "graph" with facet author(10);
searcher := search Entity matching title: "graph" with facets author(10), rating([* to 1],[2 to 3},[3 to 4},[4 to *]);
```
Specify enabled facets. These can be discrete or range facets

## Retrieving facets: 'field facets from searcherExp'
```
facets := author facets from s;
```
Returns a list: List<Facet> with the facets for the specified field. Facet objects have the following boolean functions available, for example to apply different styling on the variety of facet states:

 *   _f.isSelected()_: is this facet selected, i.e. filtered?
 *   _f.isMust(), f.isShould(), f.isMustNot()_: check the filter behaviour of this facet.

## Filtering on facet
```
searcher := ~searcher with filter(s) selectedDateFacet.must(), selectedPriceFacet.must();
```
Previously returned facets can be used to narrow the search results. The behaviour of the facet (must, should, mustnot) can be set on the facet object itself (should by default).

## Namespace scoping: 'in namespace e1'
```
searcher := search Entity matching title: "graph" in namespace "science";
```
When using search namespaces, restricting a search to a single namespace is done using the _in namespace_ construct followed by a String-compatible expression.


## Search data using native java instead of search language (some expert features)

The searcher class that is created for the example `Message` entity is `MessageSearcher`. The first advantage of using this searcher instead of the generated functions is the ability to interact with the searcher, for further refinements to the search query, or to get information like the total number of results, or time that was needed to perform the search.
```
page searchPage(query : String) {
  var searcher := MessageSearcher().query(query);
  var results := searcher.results();
  var searchTime := searcher.searchTime(); //String
  
 "You searched for '" output(searcher.query()) "', " output(searcher.count()) " results found in " output(searchTime) "."
  
  if(searcher.count() > 0) {
	showResults(results)
  }
}
template showResults(results : List<Message>) {
  //code to view results
}	
```

The available searcher functions generated for each searchable entity are:

## (Dis)Allow use of Lucene in query and filter values

(see [here](http://lucene.apache.org/java/3_1_0/queryparsersyntax.html))
```
allowLuceneSyntax(allow : Bool) : EntitySearcher
```

## OR/AND terms in user queries by default
OR is the default.
```
defaultAnd() : EntitySearcher
defaultOr() : EntitySearcher
```

## Filter results by field value, get filter value ##
```
addFieldFilter(field : String, value : String) : EntitySearcher
getFieldFilterValue(field : String) : String
getFilteredFields() : List<String>
removeFieldFilter(field : String)
clearFieldFilters()
```

## Get spell/autocomplete suggestions

The field(s) parameters specify which search field(s) to use for suggestions. 'limit' controls the max number of suggestions to retrieve. Additionally the namespace can be specified, if used. For spell suggestions the accuracy [0..1] can be set
```
static autoCompleteSuggest(toComplete : String, field : String, limit : Int) : List<String>
static autoCompleteSuggest(toComplete : String, namespace : String, field : String, limit : Int) : List<String>
static autoCompleteSuggest(toComplete : String, fields : List<String>, limit : Int) : List<String>
static autoCompleteSuggest(toComplete : String, namespace : String, fields : List<String>, limit : Int) : List<String>
static spellSuggest(toCorrect : String, fields : List<String>, accuracy : Float, limit : Int) : List<String>
static spellSuggest(toCorrect : String, namespace : String, fields : List<String>, accuracy : Float, limit : Int) : List<String>
static spellSuggest(toCorrect : String, field : String, accuracy : Float, limit : Int) : List<String>
static spellSuggest(toCorrect : String, namespace : String, field : String, accuracy : Float, limit : Int) : List<String>
```

## In/Decrease the impact of a search field in ranking of results by boosting at query-time
```
boost(field : String, boost : Float) : EntitySearcher
```

## Faceting on a search field

The <code>max</code> parameter defines the maximum facets to collect for that field. For range facets, the ranges are encoded as String in the same format as range queries. Multiple ranges can be specified concatenated, optionally seperated with a symbol like white space or comma but that's not required."</code>

```
enableFaceting(field : String, max : Int) : EntitySearcher
enableFaceting(field : String, rangesAsString : String) : EntitySearcher
getFacets(field : String) : List<Facet>
addFacetSelection(facet : Facet) : EntitySearcher
addFacetSelection(facets : List<Facet>) : EntitySearcher
getFacetSelection() : List<Facet>
getFacetSelection(field : String) : List<Facet>
removeFacetSelection(facet : Facet) : EntitySearcher
clearFacetSelection() : EntitySearcher
clearFacetSelection(field : String) : EntitySearcher
```

## Specify search field(s) to use for query or range
```
field(field : String) : EntitySearcher
fields(fields : List<String>)] : EntitySearcher
```

## Specify offset and number of results (for pagination)
```
setOffset(offset : Int) : EntitySearcher
setLimit(limit : Int) : EntitySearcher
```

## Hit highlighting
Highlight matched tokens using the analyzer from the specified search field in a given text, optionally specifying a pre- and posttag (bold by default), number of fragments, fragment length and fragment separator.
There are 4 types of highlight methods. Replace highlight with the version that is suitable for you:
 
 * _highlight_ - highlights normal text, trying to find matches by analyzing at most 50*1024 characters of the given text.
 * _highlightLargeText_ - same as normal _highlight_, but without limit on the characters it analyzes, therefore it _may_ need some more cpu time.</b>
 * _highlightHTML_ - same as normal _highlight_, but it leaves HTML tags intact such that matches in HTML tags are ignored. Used for highlighting text in HTML markup.
 * _highlightLargeHTML_ - same as _highlightHTML_, but without limit on the characters it analyzes, therefore it _may_ need some more cpu time

```
highlight(field : String, toHighlight : String) : String
highlight(field : String, toHighlight : String, preTag : String, postTag : String) : String
highlight(field : String, toHighlight : String, preTag : String, postTag : String, nOfFrgmts : Int, frgmtLength : Int, frgmtSeparator : String) : String
```

## Find similar entities based on text fragment
Just like an ordinary query, first specify the fields using the <code>field(s)</code> function

```
moreLikeThis(text : String) : EntitySearcher
```

## Set/get the current text query
Note: Query text from the first specified query is returned in case multiple queries are combined using boolean queries.
```
getQuery() : String
query(queryText : String) : EntitySearcher
```

## Sort results by field ascending or descending
```
sortDesc(field : String) : EntitySearcher
sortAsc(field : String) : EntitySearcher
clearSorting() : EntitySearcher
```

## Range query, _start_ and _end_ can be type of String, Int, Float and Date/DateTime/Time. _start_ and _end_ are included by default
```
range(start, end) : EntitySearcher
range(start, end, includeMin : Bool, includeMax : Bool) : EntitySearcher
```

## Set/get namespace
```
setNamespace(ns : String) : EntitySearcher
getNamespace() : String
removeNamespace() : EntitySearcher
```

## Get the list of results
```
results() : List<Entity>
```

## Get the number of results
```
count() : Int
```

## Get the search time
```
searchTime() : String
searchTimeMillis() : Int
searchTimeSeconds() : Float
```

## Filters
Filters are an efficient way to filter search results, because they are cached. If you expect to perform many queries using the same filter (like only showing `Message`s in a specific category), using a filter is the way to go:

```
MessageSearcher.query(userQuery).addFieldFilter("category","humor")
```

or

```
search Message matching userQuery with filter category:"humor"
```

To get the value of a previously added field filter, use the `getFieldFilterValue(field : String)` method.

## Search namespaces
Search namespaces become usefull if you want to allow searches on entities with some specific property value. For example searching `Message`s by category in the above example.
Namespaces have some advantages over using field filters. An index is created for each namespace separately, instead of one for all entities of that type. Since the indexes are used as input for auto completion and spell checking, the use of namespaces enables suggestion services scoped to one, or all, namespace(s). 

## Result highlighting

## Spell checking

## Auto completion

## Faceted search
Facets can be displayed in many contexts. For example, when displaying a list of products, you want the product categories to be displayed as facets.
Any searchable property can be used for faceting. The values, as they appear in the search index, are used for faceting. So if you use the default analyzer for the category property of Product, categories containing white spaces are not treated as single facet value. For this to work you need to define an additional field which doesn't tokenize the value of the property, for example by indexing this property untokenized:

```
entity Product{
  name       : String
  categories : Set<Category> (inverse=Category.products)

  search mapping{
    name
    categories
  }
}
entity Category {
  name     : String
  products : Set<Product>

  search mapping{
    name using none //or 'name using no' in v1.2.9.0
  }
}
```

Facets can be retrieved through the use of a searcher.
You first need to specify the facets you want to use by enabling them in the searcher. 
A typical example is to display facets in the search results:

(updated April 5th)
```
template searchbar(){
  var query := "";
  form {
    input(query)
    submit action{
        //construct a searcher and enable faceting on tags.name, limited to 20 top categories
        //more facets can be enabled by separating the field(topN) facet definitions by a comma
        var searcher := search Product matching query with facets categories.name(20);
        return search(searcher);} {"search"}
  }
}

page search(searcher : ProductSearcher){
    var results : List<Product> := results from searcher;
    var facets  : List<Facet>   := categories.name facets from searcher;

    header{"Filter by product category:"}
    for(f : Facet in facets){
        facetLink(f, searcher)
    }separated-by{" "}

    showResults(results)
}
template facetLink(facet: Facet, searcher: ProductSearcher){
    submitlink narrow(facet){ if(facet.isSelected()){"+"} output(facet.getValue()) }"(" output(facet.getCount()) ")"

    action narrow(facet : Facet){
      if (facet.isSelected()) { searcher.removeFacetSelection(facet); } else { ~searcher matching facet.must(); }
      goto search(searcher);
    }
}
```
