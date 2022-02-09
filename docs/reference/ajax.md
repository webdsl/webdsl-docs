# Ajax

WebDSL provides ajax operations which allow you to easily replace an element or group of elements in a page without reloading the entire page. These operations can be used as statements inside actions or functions. 

## Ajax Operations

```
replace(target, templatecall);
append (target, templatecall);
clear  (target);

restyle (target, String);
relocate(pagecall);
refresh();

visibility(target, show/hide/toggle);

runscript(String);
```

The most commonly used operation is replace, for example:

    action someaction() {
      replace(body, showUser(user)); 
    }

This will replace the contents of an element or placeholder (see ajax targets section below) that has id 'body' with the output of the templatecall `showUser(user)`.

`runscript` provides a way to interface with arbitrary Javascript code. Put .js files in a javascript directory in the root of your project and include it using `includeJS` in a template, e.g. `includeJS("sdmenu.js")`.

Example: Moving a div around using JQuery animate:

    runscript("$('"+id+"').animate({left:'"+x+"', top:'"+y+"'},1000);");

The *refresh* action is the default action; when no other interface changing operations are executed the browser will just refresh the current page. For example an input form which submits to a completely empty action results in the data being saved (default behavior) and the page being refreshed (default behavior). 

## Ajax Targets

There are three ways to target an ajax operation. 
*target* can either be 

* the name of an  existing template
* the name of a placeholder 
* or the *id* attribute of an object.
* a String expression that creates the id, e.g. using a related entity object's id property. 

Placeholder with non-ajax default content:

    placeholder leftbar { "default content" /* non-ajax default elements */ }

Placeholder with ajax default content:

    placeholder leftbar ajaxTemplateCall() /* call to ajax template*/

Id attribute example:

    table[id := myfirsttable] { /* non-ajax default elements */ }

When a template is used as target in an ajax operation, it must be declared with the `ajax` modifier.

Example:

    define testtemplate(p:Person){
      placeholder testph{ "no details shown" }
      submit("show details",show())[ajax]
      action show(){
        replace(testph,showDetails(p));
      }
    }
    define ajax showDetails(person:Person){
      " name: " output(person.name)
    }

Since an ajaxtemplate results in an extra entry point at the server, it must be explicitly opened when access control is enabled:

    rule ajaxtemplate showDetails(p:Person){true}

## DOM Event Handling

To invoke actions when an HTML event is fired, for example when pressing a key, event attributes can be defined on elements. The syntax of such an attribute is:
```
<event name> := <action call>
```
<a href="http://w3schools.com/tags/ref_eventattributes.asp">W3schools.com</a> provides an overview of all available events. 

Example:
```
"quicksearch: " 
input(search)[onkeyup := updatesearch(search)]
```

The result is that the `updatesearch` action is invoked on the server.

## Forms and Ajax

Typically you should not make a form cross an ajax placeholder. The server considers ajax templates as self-contained components similar to pages.

Example of proper usage:

    define demo(){
      placeholder test()
    }
    define ajax test(){
      form{ input(someGlobal.name) submit action{} {"save"} }
    }

Example of incorrect usage (the submit will be contained in a form on the client but not on the server):

    define demo(){
      form{
        placeholder test()
      }
    }
    define ajax test(){
      input(someGlobal.name) submit action{} {"save"}
    }

In some cases interaction between a regular form and ajax operations is not an issue, e.g. when the ajax template does not contain any input elements. The most common case is rendering validation messages in the form, this behavior is provided in the WebDSL library, see next section.

## Ajax Input Validation

There are prebuild library components for creating inputs with ajax validation responses.

A simple example:

    define demo(){  
      var s := "test"
      form{    
        inputajax(s)
        submit action{ log(s); } {"log"}
      }
    }
