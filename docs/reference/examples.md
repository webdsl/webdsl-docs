# Examples

Below are larger examples of WebDSL applications than those found on the other manual pages.

## Minimal Access Control Example

	application minimalac
	
	  entity User {
	  	name : String
	  	password : Secret
	  }
	  
	  init{
	  	var u := User{ name := "1" password := ("1" as Secret).digest()  };
	  	u.save();
	  }
	  
	  page root(){
	  	authentication()
	  	" "
	  	navigate protectedPage() { "go" }
	  }
	  
	  page protectedPage(){ "access granted" }
	  
	  principal is User with credentials name, password
	  
	  access control rules
	  
	    rule page root(){true}
	    rule page protectedPage(){loggedIn()}

## Example Native Interface

A simple application that shows the public timeline of Twitter using [Twitter4J](http://twitter4j.org/). Example project code is available [here](https://webdsl.org/twitterapp.zip)


Screenshot of result:
<br />
<img src="https://webdsl.org/twitterreader.png" />
<br />
<br />
Project files:
<br />
<img src="https://webdsl.org/twitter1.png" />
<br />
WebDSL application with native class interface declaration:

	application exampleapp
	
	page root() {
	  output(TwitterReader.read(null,null))
	}
	
	native class nativejava.TwitterReader as TwitterReader {
	  static read(String,String):List<String>
	}

Implementation of TwitterReader.java:

	package nativejava;
	
	import java.util.*;
	
	import twitter4j.Status;
	import twitter4j.Twitter;
	import twitter4j.TwitterException;
	import twitter4j.TwitterFactory;
	public class TwitterReader{
	
	    public static List<String> read(String twitterID,String twitterPassword){
	        //The factory instance is re-useable and thread safe.
	        Twitter twitter = new TwitterFactory().getInstance(twitterID,twitterPassword);
	        List<String> result = new LinkedList<String>();
	        List<Status> statuses;
	        try {
	            statuses = twitter.getPublicTimeline();
	            for (Status status : statuses) {
	                result.add(status.getUser().getName() + ":" + status.getText());
	            }
	        } catch (TwitterException e) {
	            e.printStackTrace();
	        }
	        return result;
	    }
	}

## Ajax Example Custom Validation

This example will show a complex form that uses Ajax for custom data validation.

<object width="550" height="400">
<param name="movie" value="http://www.st.ewi.tudelft.nl/~danny/webdsl-ajax-validation.swf">
<embed src="http://www.st.ewi.tudelft.nl/~danny/webdsl-ajax-validation.swf" width="550" height="500">
</embed>
</object>

The full project source of this example is located here:

[https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed-web/manual/ajax-form-validation/](https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed-web/manual/ajax-form-validation/)

### Important Note 1: inverse annotations

Inverse annotations can cause problems due to save cascading in WebDSL, if an inverse is made with an entity in the database, then your temporary entity will be automatically saved in the database as well.

### Important Note 2: data validation

Don't use WebDSL's data validation described on the [Validation](../validation) page in combination with this example, because validation is done with custom code here. Data validation will be integrated with ajax to more easily get the result that is implemented in this example.

We're going to create an edit page for a `Person` entity:

	entity Person {
	  fullname : String
	  username    : String (name)
	  parents     : Set<Person>
	  children    : Set<Person>
	}

The name annotation indicates that the `username` is used to refer to the Person entity in select inputs, such as those for the `parents` and `children` property, see the [Name Property](../entities#name-property) page.

The `root` page includes a `personedit` template and passes it a new `Person` object.

	page root() {
	  main()
	  template body() {
	    personedit(Person{})
	
	  }
	}

The `personedit` template provides the form that checks values whenever changes occur. The various `placeholder` elements provide a location to insert error messages. The actual checks are encapsulated in functions, this allows the save action to easily do a server-side check before saving the new Person object. 
	
	template personedit(p:Person){
	  form{
	    par{
	      label("username: "){ input(p.username)[onkeyup := action{ checkUsername(p); checkUsernameEmpty(p); checkFullname(p); }] }
	      placeholder pusernameempty { }
	      placeholder pusername { }
	    }
	    par{
	      label("fullname: "){ input(p.fullname)[onkeyup := action{ checkFullname(p); checkFullnameEmpty(p); } ] }
	      placeholder pfullnameempty { }
	      placeholder pfullname { }
	    }
	    par{
	      label("parents: "){ input(p.parents)[onchange := action{ checkParents(p); } ] }
	      placeholder pparents { }
	    }
	    par{
	      label("children: "){ input(p.children)[onchange := action{ checkParents(p); } ] }
	    }
	    submit save() [ajax] {"save"} //explicit ajax modifier currently necessary in non-ajax templates to enable replace. A warning is shown in the log if this is missing.
	  }	
	  action save(){ 
	    // made an issue requesting & operator :)
	    var checked := checkUsernameEmpty(p);
	    checked := checkUsername(p) && checked;
	    checked := checkFullname(p) && checked;
	    checked := checkParents(p) && checked;
	    checked :=  checkFullnameEmpty(p) && checked;
	    if(checked){
	      p.save();
	      return root();
	    } 
	  }
	}

The function definitions perform the check, and also update the placeholders (note that placeholder names are currently global in the application). They also return the result as a boolean, so the functions can be reused in the save action. `replace` calls perform the insertion of templates into placeholders, in this case the templates are just creating messages.
	
	function checkUsernameEmpty(p:Person):Bool{
	  if(p.username != ""){ 
	    replace(pusernameempty, empty());
	    return true;
	  } 
	  else {
	  replace(pusernameempty, mpusernameempty());
	  return false; 
	  }
	}
	function checkUsername(p:Person):Bool{
	  if((from Person as p1 where p1.username = ~p.username).length == 0)){ 
	    replace(pusername, empty());
	    return true;
	  } 
	  else {
	  replace(pusername, mpusername(p.username));
	  return false; 
	  }
	}
	
	function checkFullnameEmpty(p:Person):Bool{
	  if(p.fullname != ""){ 
	    replace(pfullnameempty, empty());
	    return true;
	  } 
	  else {
	  replace(pfullnameempty, mpfullnameempty());
	  return false; 
	  }
	}
	function checkFullname(p:Person) :Bool{
	  if(p.username != p.fullname) { 
	    replace(pfullname, empty());
	    return true;
	  } 
	  else{
	    replace(pfullname, mpfullname());
	    return false; 
	  }
	}


The templates for the messages are shown below. An errorclass template is used to wrap all the errors in the same div tag with special error class, to provide a hook for CSS styling. Templates that are used in replace actions have to be declared as `ajax` template. When access control is enabled the ajax templates can be protected with the `ajaxtemplate` rule type.


	template errorclass(){
	  <div class="error"> elements() </div>
	}
	ajax template empty(){ "" }
	ajax template mpusername(name: String){ errorclass{ "Username " output(name) " has been taken already" } }
	ajax template mpusernameempty(){ errorclass{ "Username may not be empty" } }
	ajax template mpfullname(){ errorclass{ "Username and fullname should not be the same" } }
	ajax template mpfullnameempty(){ errorclass{ "Fullname may not be empty" } }
	ajax template mpparents(pname:String,names : List<String>){ 
	  errorclass{ 
	    "Person" 
	    if(names.length > 1){"s"}
	    " " 
	    for(name: String in names){
	      output(name)
	    } separated-by {", "}
	    " cannot be both parent and child of " output(pname)
	  }
	}

This app includes some CSS for top-aligned labels ([http://css-tricks.com/label-placement-on-forms/](http://css-tricks.com/label-placement-on-forms/)), the errors are shown on new lines and in red. 

	label {
	  clear:both;
	  float:left;
	  margin:10px 0 2px 0;
	}
	input, select {
	  clear:both;
	  float:left;
	}
	#errorclass {
	  color: red;
	  clear: both;
	  float: left;
	  margin:2px 0 0 0;
	}
	input[type="button"]{
	  clear: both;
	  float: left;
	  margin: 20px 0 10px 0; 
	}


### Edit instead of Create

Since the example used a new Person entity, the `save` controls whether the object is persisted. If the entity was already in the database, and this is an edit page, then the save wouldn't be necessary to persist changes. Unfortunately this has the side-effect that all intermediate submits (on every change) already persist the changes automatically. One way to work around this issue create a copy of the entity and use that for data binding. Instead of the `save()` call, the code needs to put the changes back into the real persisted entity.
	
The editpage, in this case a global entity var is passed in, to demonstrate changing an entity that is persisted.

	page edit(){
	  main()
	  template body() {
	    personedit(pAlice)
	  }
	}

The template is shown below, unchanged parts are left out. A template var that copies the original values is used for data binding, in the save action the changes are placed in the real person object. The `save` call is not necessary for edits, but now the template works correctly for both edit and create actions.


	template personedit(realp:Person){
	  var p := Person{ username := realp.username fullname := realp.fullname children := realp.children parents := realp.parents};
	  form{
	    par{
	      label("username: "){ input(p.username)[onkeyup := action{ checkUsername(p,realp); checkUsernameEmpty(p); checkFullname(p); }] }

	     ...
	
	  action save(){ 
	    // made an issue requesting & operator :)
	    var checked := checkUsernameEmpty(p);
	    checked := checkUsername(p,realp) && checked;
	    checked := checkFullname(p) && checked;
	    checked := checkParents(p) && checked;
	    checked :=  checkFullnameEmpty(p) && checked;
	    if(checked){
	      realp.username := p.username;
	      realp.fullname := p.fullname;
	      realp.parents := p.parents;
	      realp.children := p.children;
	      realp.save(); // does nothing in the case of an update
	      return root();
	    } 
	  }
	}

One of the checks needs to change, because the entity might be already in the database now.

	function checkUsername(p:Person, realp:Person):Bool{
	  var matches := from Person as p1 where p1.username = ~p.username;
	  if(matches.length == 0 || (matches.length == 1 && matches[0] == realp)){ 
	    replace(pusername, empty());
	    return true;
	  } 
	  else {
	  replace(pusername, mpusername(p.username));
	  return false; 
	  }
	}



The full project source of this example is located here:

[https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed-web/manual/ajax-form-validation/](https://svn.strategoxt.org/repos/WebDSL/webdsls/trunk/test/succeed-web/manual/ajax-form-validation/)
