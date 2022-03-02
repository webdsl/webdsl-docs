# Validation

Checking user inputs and providing clear feedback is essential for the usability of web applications. WebDSL allows declarative specification of such input validation rules using the *validate* feature.

Validation rules in WebDSL are of the form *validate(e,s)* and consist of a Boolean expression *e* to be validated, and a String expression *s* to be displayed as error message. Any globally visible functions or data can be accessed as well as any of the properties and functions in scope of the validation rule context. 

Value well-formedness checks (e.g. whether the user enters a valid integer in an Int input) are added automatically to each input field.

Validation can be specified on entities in property annotations:

    entity User { 
      username : String (id, validate(isUniqueUser(this), "Username is taken")) 
      password : Secret (validate(password.length() >= 8, "Password needs to be at least 8 characters") 
      , validate(/[a-z]/.find(password), "Password must contain a lower-case character") 
      , validate(/[A-Z]/.find(password), "Password must contain an upper-case character") 
      , validate(/[0-9]/.find(password), "Password must contain a digit"))
      email : Email
    } 
    extend entity User { 
      username(validate(isUniqueUser(this),"Username is taken")) 
      password(validate(password.length() >= 8, "Password needs to be at least 8 characters") 
      ,validate(/[a-z]/.find(password), "Password must contain a lower-case character") 
      ,validate(/[A-Z]/.find(password), "Password must contain an upper-case character") 
      ,validate(/[0-9]/.find(password), "Password must contain a digit")) 
    } 

Validation can be specified directly in pages:

    page editUser( u: User ){ 
      var p: Secret; 
      form { 
        group( "User" ){ 
          label( "Username" ){ input( u.username ) } 
          label( "Email" ){ input( u.email ) } 
          label( "New Password" ){ 
            input( u.password )
          } 
          label( "Re-enter Password" ){ 
            input( p ){ 
              validate( u.password == p, "Password does not match" ) 
            } 
          } 
          submit action{} { "Save" }
        } 
      }
    }

Validation can be specified in actions:

    page createGroup { 
      var ug := UserGroup{} 
      form { 
        group( "User Group" ){ 
        label( "Name" ){ input( ug.name ) } 
        label( "Owner" ){ input( ug.owner ) } 
        submit save() { "Save" }

        action save() { 
          validate( ug.owner != null, "A group must have an owner" ); 
          return userGroup( ug ); 
       }
     } 

## Customizing Validation Output

Validation output can be customized by overriding the templates used to display validation messages. Currently, there are 4 global validation templates:

    ignore-access-control template errorTemplateInput(messages : [String])

Displays validation message related to an input.  

    ignore-access-control template errorTemplateForm(messages : [String])

Displays validation message for validation in a form.

    ignore-access-control template errorTemplateAction(messages : [String])

Displays validation message for validation in an action.

    ignore-access-control template templateSuccess(messages : [String])

Displays validation message for success messages.

When overriding these validation templates, use an `elements` templatecall to refer to the element being validated.

Example:

    ignore-access-control template errorTemplateInput(messages : [String]){
      elements
      for(ve: String in messages){
        output(ve)
      }     
    }

## Ajax Validation

WebDSL provides input components that validate the inputs using ajax.

built-in value types:

    inputajax(String/Secret/URL/Email/Text/WikiText)
    inputajax(Int/Bool/Float/Long)

reference types:

    inputajax(Entity / [Entity] / {Entity})
    selectajax(Entity / {Entity})
    radioajax(Entity)

provide selection options:

    inputajax(Entity / [Entity] / {Entity}  , [Entity])
    selectajax(Entity / {Entity}            , [Entity])
    radioajax(Entity                        , [Entity])

Selection options can also be provided using the `allowed` annotation on an entity property.
Example:

    entity Person{
      parent : Person (allowed=from Person as p where p != this)
    } 
