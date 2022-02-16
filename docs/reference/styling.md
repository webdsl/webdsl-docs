# Styling

Styling of WebDSL pages is done using CSS. In the application directory add the following directory and file:

    stylesheets/common_.css

This CSS file will be automatically included when deploying the application. Other CSS files can be included using `includeCSS` (in this example the included file is located at stylesheets/jquery-ui.css):

    includeCSS("jquery-ui.css")

When the application is deployed in the Eclipse plugin you can edit the CSS file directly in the tomcat directory (don't forget to also save the CSS file back to your project): 
  
    WebContent/stylesheets/common_.css  

For deployment to external tomcat this directory is:

    tomcat/webapps/appname/stylesheets/common_.css  

The Firebug add-on for Firefox can be very helpful in figuring out the page structure, other browsers have similar development tools.

Explicit hooks for CSS can be added using the XML embedding:

    template someTemplate(){ 
      <div class="mydiv">
        "content of mydiv"
      </div>
    }

Note that the *"mydiv"* is a WebDSL expression, so this could also be stored in an entity and retrieved using a field access:
  
    <div class=user.cssclass>

Classes for styling can also be added to a template call (separate from the regular arguments):

    input[class="mynameinput"](u.name)

If you want to define your own template that takes such extra arguments, use `all attributes`:

    page root(){
      someOtherTemplate[class="importantdiv"]{ "content" }
    }

    template someOtherTemplate(){ 
      <div all attributes>
        elements
      </div>
    }

The `span` template modifier adds a span around a template, which can then be used as a hook for CSS:

    template span spanTemplate(){ "span around me" }
