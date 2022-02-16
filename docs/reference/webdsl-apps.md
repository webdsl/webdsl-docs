# WebDSL Apps

## The structure of a WebDSL application

WebDSL application are organized in _*.app_ files. Each .app file has a header, that either declares the name of a _module_ or the name of an _application_. The declared name should be identical to the filename. Each _application_ needs an .app file that declares the name of the application. This is the name refered to in the application.ini file (see below). 

An application can be organized in different _modules_. In a typical .app file the header is followed by a list of import statements, which contain a path to other modules (without extension). In this way your application can be separated over several files, and modules can be reused. 

Within .app files one can define sections. A section is merely a label to identify the structure of a file. Most section names have no influence on the program itself, some have however, for example in styling definitions.

The real contents of a .app file are a list of definitions. This might be page-, template-, action- or entity definitions. Other kinds of definitions might be introduced by WebDSL modules. A module might either refer to a module of an WebDSL application, or to an module of the WebDSL compiler itself. In this case the latter one is refered to. Those definitions will be examined in detail in the next chapters. 

A very simple application might look like:

**HelloWorld.app:**

    application HelloWorld

    imports MyFirstImport

    section pages

    page root () { 
        "hello world" 
        IAmImported() 
    }

**MyFirstImport.app:**

    module MyFirstImport
  
    template IAmImported() { 
        spacer 
        "I am imported from a module file" 
    }

In the second file the section declaration is omitted, since an application may start with a list of declarations as well. The page that is shown when no page is specified (e.g. when visiting http://localhost:8080/yourapp) is named "root" and has no arguments.