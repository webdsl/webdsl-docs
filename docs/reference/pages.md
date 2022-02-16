# Pages

Pages in WebDSL can be defined using the following construct:  
  
     page [pagename]( [page-arguments]* ){ [page-elements]* }

There are basic output elements for structure and layout of the page, such as `title` and `header`. 

Example:

    page root {
      title { "Page title" }
      section {
        header{ "Hello world." }  
        "Greetings to you."
      }
    }

## Page Parameters

Pages can have parameters, and `output` is used for inserting data values. 

Example:

    page user(u : User) {
      "The name of this user is " output(u.name)
    }

## Input Forms

The `form` element in combination with `submit` is used for submitting data. `input` elements perform automatic data binding upon submit. For more information about forms, go to the [[page(form)|Form page]].

Example:

    page editUser(u:User){
      form{
        input(u.name)
        submit action{} { "save" } 
      }
    }

## Templates

Pages can be made reusable by declaring them as template, and calling them from other pages or templates.

Example:

    template common(){
      header{ "my page" }
    }
    page root(){
      common()
    }

## Output

The `output(<expression>)` template call is used to display a value in a page. It can also be used with Entity type expressions, and collections.

Example:

    page user(u:User){
      output(u)
    }

The `output` template can be customized for each entity type.

Example:

    template output(u:User){
      "user with name: " output(u.name)
    }


## Navigate

    navigate <page call> { <page element*> }

Link to a page. For example: 

    page news() { "News" }

## Title

Declares the title of the current page.

    title { element* }

## Description

Declares the description of a page (not visible, added as description meta tag in the head section of a page). This data is [often viewed](http://support.google.com/webmasters/bin/answer.py?hl=en&answer=35624#1) in search result snippets. Introduced in WebDSL 1.3.0.

    description { element* }

## Section

    section { element* }

Indicate sections in a document; may be nested. May include a 

    header { element* }

element that indicates the section title.

## Image

    image ( <string with relative or absolute path to image> )

Displays an image. Images placed in an "images" folder in the root directory of your application will be automatically copied during deployment.

Example:

    page root(){
      image("https://webdsl.org/webdslorg/images/WebDSL-small.png") 
      image("/images/WebDSL-small.png") 
    }

## Lists

Lists can be created with the `list` and `listitem` elements.

Example:

    list {
      listitem { "Milk" }
      listitem { "Potatoes" }
      listitem { "Cheese (lots)" }
    }

## Tables

Tables can be created with the `table`, `row`, and `column` elements.

Example:

    table  {
      row { column{ "Username" } column{ output(user.name) } }
      row { column{ "Password" } column{ "it's a secret" } }
    }

## Block

    block{ <page element*> }
    block(String){ <page element*> }

Groups text; optionally defines a class for referencing in CSS. Results in a &lt;div> element in HTML.

## Templates

Templates enable reuse of page elements. For example, a template for a footer could be:

    template footer() { All your page are belong to us. }

This template can be included in a page with a template call: 

    page example(){
      footer
    }

Like pages, templates can be parameterized.

    template edit(g:Group){
      form {
        input(g.members)
        action("save",action{})
      } 
    }

    page editGroup(g:Group){
      edit(g)
    }

### Overloading

While pages must have unique names, templates can be overloaded. The overloading is resolved compile-time, based on the static types of the arguments.

    template edit(g:Group){...}
    template edit(u:User){...}

    page editGroup(g:Group){
      edit(g)
    }

### Dynamically scoped templates redefinitions

Template definitions can be redefined locally in a page or template, to change their meaning in that specific context. All uses are replaced in templates called from the redefining template.

    template main{
      body()
    }
    template body(){
      "default body"
    }
    page root(){
      main
      template body(){
        "custom body"
      }
    }


## For-loop

Iterating a collection of entities or primitives can be done using a for loop. There are three types of for loops for templates:

**For**

        for(id:t in e){ elem* }
This type of for loop iterates the collection produced by expression e, which must contain elements of type t. The elements in the collection are accessible through identifier id.  

The collection can be filtered:
        
        for(id:t in e filter){ elem* }

**ForAll**

This for loop iterates all the entities in the database of type t. These can also be filtered. Note that it is more efficient to retrieve the objects using a filtering query and use the regular for loop above for iteration. 
    
        for(id:t){ elem* }
        for(id:t filter){ elem* }

**For Count**

This for loop iterates the numbers from e1 to e2-1.

        for(id:Int from e1 to e2){ elem* }
    
**For Separator**

All three template for loops can be followed by a separated-by declaration, which will separate the outputs from the for loop with the declared elem*.

        separated-by{ elem* }

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

## XML Embedding

XML fragments can be embedded directly in templates. This allows easy reuse of existing XHTML fragments and CSS. For example: 

    template main() {
      <div id="pagewrapper">
        <div id="header">
          header()
        </div>
        <div id="footer">
          <p />"powered by " <a href="https://webdsl.org">"WebDSL"</a><p />
        </div>
      </div>
      <some:tag />
    }

While the name and attribute names are fixed, the attribute values can be any WebDSL expression that produces a string:

    template test(i : Int) {
      <div id="page" + "wrapper" + i />
    }

## Include CSS

The `includeCSS(String)` template call allows you to include a CSS file in the resulting page. CSS files can be included in your project by placing them in a stylesheets/ directory in the project root.

Example 1:

    page root() {
      includeCSS("dropdownmenu.css")
    }

It is also possible to include a CSS file using an absolute URL. 

Example 2:

    page root() {
      includeCSS("https://webdsl.org/stylesheets/common_.css")
    }

The media attribute can be set by passing it as second argument in `includeCSS(String,String)`

Example 3:

    page root(){
      includeCSS("https://webdsl.org/stylesheets/common_.css","screen")
    }


## Include Javascipt

The `includeJS(String)` template call allows you to include a javascript file in the resulting page. Javascript files can be included in your project by placing them in a javascript/ directory in the project root.

Example 1:

    page root() {
      includeJS("sdmenu.js")
    }

It is also possible to include a Javascript file using an absolute URL. 

Example 2:

    page root() {
      includeJS("http://ajax.googleapis.com/ajax/libs/jquery/1.2.6/jquery.min.js")
    }

## Page Not Found Error

When an invalid URL is being requested from a WebDSL application, the default response is to give a 404 error. To customize this error page, define a `pagenotfound` page in your application.

Example:

    page pagenotfound() {
      title{ "myapp / page not found (404)" }
      main()
      template body() {
        par{ "That page does not exist!" }
        par{ "Maybe you can find what you are looking for using the search page." }
      }
    }
    

## Raw output

By default, any template content will be escaped, if you want to include a string directly in the page source use rawoutput.

Example:

    rawoutput{"&nbsp;"}

## HTML Element Attributes on Template Call

Setting HTML element attributes is supported for calls to built-in templates, the syntax is as follows:

    templatename(...)[attrname=e, ...]{ ... }
 
`attrname` is an attribute name, and `e` is a webdsl expression such as `"foo"` or `"foo"+bar`.

Example:

    page root(){ 
      var somevalue := "lo"
      image("/images/logosmall.png")[alt = somevalue+"go", longdesc = "blablabla"]
      navigate root()[title = "root page"]{ "root" }
    }

## Override Modifier

Template and page definitions can be overridden using the `override` modifier, e.g. to override a built-in page such as `pagenotfound`:

    override page pagenotfound(){
      "page does not exist!"
    }

## SQL Logging for Page Rendering

add `?logsql` after the URL of a page to get a log of all the SQL queries executed to render that page

for applications with access control enabled, accessing this log is disabled by default, it can be enabled using an access control rule:

    rule logsql { check }

e.g.

    rule logsql { principal.isAdmin }
