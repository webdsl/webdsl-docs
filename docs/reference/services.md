# Services

WebDSL includes a simple way to define string and JSON-based webservices.

## JSON API

The JSON interface is defined as follows:

```
  native class org.json.JSONObject as JSONObject {
    constructor()
    constructor(String)
    get(String) : Object
    getBoolean(String) : Bool
    getDouble(String) : Double
    getInt(String) : Int
    getJSONArray(String) : JSONArray
    getJSONObject(String) : JSONObject
    getString(String) : String
    has(String) : Bool
    names() : JSONArray
    put(String, Object)
    toString() : String
    toString(Int) : String
  }
  
  native class org.json.JSONArray as JSONArray {
    constructor()
    constructor(String)
    get(Int) : Object
    getBoolean(Int) : Bool
    getDouble(Int) : Double
    getInt(Int) : Int
    getJSONArray(Int) : JSONArray
    getJSONObject(Int) : JSONObject
    getString(Int) : String
    length() : Int
    join(String) : String
    put(Object)
    remove(Int)
    toString() : String
    toString(Int) : String
  } 
```

Example use in WebDSL:

```
function myJsonFun() : String {
    var obj := JSONObject("{}");
    obj.put("name", "Pete");
    obj.put("age", 27);
    return obj.toString();
    // Will return '{"name": "Pete", "age": 27}'
}
```

## Defining services

A service is simply a WebDSL function that uses the `service` keyword instead of `function`, you don't have to specify a return type, it will convert anything you return to a string (using `.toString()`):

```
entity Document {
  title : String (id, name)
  text  : Text
}

service document(id : String) {
  if(getHttpMethod() == "GET") {
     var doc := findDocument(id);
     var json := JSONObject();
     json.put("title", doc.title);
     json.put("text", doc.text);
     return json;
  }
  if(getHttpMethod() == "PUT") {
    var doc := getUniqueDocument(id);
    var json := JSONObject(readRequestBody());
    doc.text := json.getString("text");
    return doc.title;
  }
}
```

services are mapped to `/serviceName`, e.g. `/document`. Here's a few sample requests to test (note, these are services part of an application called "hellojson"):

```
$ curl -X PUT 'http://localhost:8080/hellojson/document/my-first-doc' \
       -d '{"text": "This is my first document"}'
my-first-doc
$ curl http://localhost:8080/hellojson/document/my-first-doc
{"text":""This is my first document","title":"my-first-doc"}
```

But, like pages, services can also have entities as arguments:

```
service documentJson(doc : Document) {
   var obj := JSONObject();
   obj.put("title", doc.title);
   obj.put("text", doc.text);
   return obj;
}
```

<!-- ## Integration with Mobl

The following zip file contains a WebDSL and a [Mobl](http://mobl-lang.org/) project, the WebDSL application provides data through a web service to the Mobl application:

[https://webdsl.org/examples/mobl-webdsl-example.zip](https://webdsl.org/examples/mobl-webdsl-example.zip)

How to use this example:

0 Get the Eclipse zip from the WebDSL site ([[selectpage(Download/WebDSLplugin)|WebDSL in Eclipse]]), which contains the WebDSL and Mobl plugins.

1 Import both projects using:
'file -> import -> exising projects into workspace'
(the testapp WebDSL project will automatically compile and deploy)

2 Open testmobl.mobl, add a space somewhere and save the file to trigger a compilation of the Mobl project.

3 In the WebDSL project, right-click import-mobl.xml and select 'run as->ant build'.
This simply copies the content of the Mobl project www directory to the WebContent directory of the WebDSL project, which contains the deployed application.

4 Go to the browser and click the link 'show mobile page'.

Note that you will need to update the project names in 'import-mobl.xml' when copying it to another project, and you should copy the 'projectname import-mobl.xml.launch' file as well. -->
