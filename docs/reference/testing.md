# Testing

Tests for the entities and functions operating on those entities can be defined in test blocks.

Example:

    test capitalizeTest {
      var u := User{ name := "alice" };
      u.capitalizeName();
      assert(u.name == "Alice");
    }
    entity User {
      name :: String
      function capitalizeName(){
        var temp := name.explodeString();
        temp.set(0,temp.get(0).toUpperCase());
        name := temp.concat();
      }
    }

The 'webdsl test appname' command builds the app and runs the tests, an error will be returned when any of the tests fail. This command will create a new application.ini and uses an in-memory H2 Database Engine.

    webdsl test myapp

If the settings in the existing application.ini can be used for testing, run the 'webdsl check' command instead.

    webdsl check

Multiple test blocks can be defined in an application, each test will run with a fresh initialization of the database, the global variables and global init blocks are handled before each test.

Use assert calls to verify the correctness of results. When the assert argument evaluates to false, the test run will show the location of the failing assert. If the assert consists of one == or != comparison, the two compared results will also be printed upon failure. The assert function has an optional second argument, a String which can be used to pass a message that will be shown when the assert fails.

Signatures:

**assert(Bool)**

**assert(Bool, String)**

Testing example: tests for built-in Text type: [text.app](https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed/text.app).

## Web testing

The resulting web application can also be automatically tested by using 

    webdsl test-web myapp

or

    webdsl check-web

where 'test-web' will automatically create an application.ini and 'check-web' will use an existing one.

These commands start up tomcat and run the tests. Tests are currently expressed by calls to WebDriver with HTMLUnit behind it (a better abstraction specific to WebDSL tests is planned, current web testing is mainly to support testing the compiler). The interface is described here (see [[selectpage(Manual/NativeClass)|Native Class]]):

    https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/src/org/webdsl/dsl/languages/test/native-classes.str

Example:

    test datavalidation {
      var d : WebDriver := HtmlUnitDriver();
    
      d.get(navigate(root()));
      assert(!d.getPageSource().contains("404"), "root page may not produce a 404 error");
    
      var elist : List<WebElement> := d.findElements(SelectBy.tagName("input"));
      assert(elist.length == 4, "expected 4 <input> elements");
    
      elist[1].sendKeys("123");
      elist[2].sendKeys("111");
    
      elist[3].click();
    
      var pagesource := d.getPageSource();
    
      var list := pagesource.split("<hr/>");
    
      assert(list.length == 3, "expected two occurences of \"<hr/>\"");
 
      assert(list[1].contains("inputcheck"), "cannot find inputcheck message");
      assert(list[2].contains("formcheck"), "cannot find formcheck message");
    
  }

Source:

    https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed-web/data-validation/validate-in-elements.app

