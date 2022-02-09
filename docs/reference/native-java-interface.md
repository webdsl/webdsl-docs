# Native Java Interface

## Native Classes

Native Java classes can be declared in a WebDSL application in order to interface with existing libraries and code. If you want to use just one native function, _see [[page(NativeFunctionInterface)|native function interface]]._

The supported elements are properties, (static) methods, and constructors. The supported types are

* *WebDSL type - Java type*
* Int - int or Integer
* Bool - boolean or Boolean
* Float - float or Float
* String - String

Both the primitive type and the object types such as int and Integer can be produced by the WebDSL call (so overloading between these types is a problem here).

Add Java classes to a nativejava/ dir next to your app file and jar files in lib/.

Example:

    native class nativejava.TestSub as SubClass : SuperClass {
      prop :String
      getProp():String
      setProp(String)
      constructor()
    }
  
    native class  nativejava.TestSuper as SuperClass  {
      getProp():String
      static getStatic(): String
      returnList(): List<SubClass>
    }

    define page root() {
      var d : SuperClass := SubClass()  
      output(d.getProp())
     
      var s : SubClass := SubClass()
      init{
        s.setProp("test");
      }
      output(s.prop)
    
      output(SuperClass.getStatic())
     
      for(a: SubClass in d.returnList()){
        output(a.prop)
      } 
    }



(Example taken from compiler tests, source 

    https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed/native-classes.app
    https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed/nativejava/TestSub.java
    https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed/nativejava/TestSuper.java

)

### Passing a native java instance as page argument
If you want an instance of your defined native java class to be passed as page (or ajax template) argument, the class should be serializable for WebDSL. From WebDSL version 1.3.0 and on, support is added for doing this by implementing the following 2 methods in your java class. (If you use a class defined in a library, you may need to extend this class with the following methods)

    public static YourClass fromParamMap( Map<String,String> paramMap )
    public final Map<String,String> toParamMap()

_note_: The keys in the returned Map may only consist of character classes [A-Z][a-z][0-9], values may hold any value as they get filtered. On deserialization, the static `fromParamMap` method is invoked and its result is cast to the type as defined in the page/java template definition.

Examples can be [found](https://webdsl.org/reposearch/doSearch/sl=100000&ns=WebDSL&op=AND&q=fromParamMap+toParamMap&lim=5&dff=repoPath%2CfileExt%2C&type=Entry&dfp=200%2C120%2C&pq=fromParamMap+toParamMap&allowlcn=false&/WebDSL/1) (notice the link ;)) in WebDSL's source code itself.