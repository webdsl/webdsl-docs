# Request Processing

Users interact with web applications through the browser. This
	process consists of request and response strings being
	exchanged between the web server and the browser. A form is defined by a response
	string, which is interpreted by the browser to produce
	components that allow user interaction. A user can fill in
	data in a text field, and press the submit button. The browser
	first collects the data from the form input fields, and
	constructs a request string to send to the web server, which
	receives the request string and parses it. Values from input
	fields can be accessed separately but are represented as
	strings. A web application bears the responsibility of
	converting these strings to actual types to be used in further
	processing of the request. In WebDSL, the conversion of request parameters is done automatically. This is the first phase of the request processing lifecycle. The request processing lifecycle consists of the following phases:

* Convert request parameters
* Update model values
* Validate forms
* Handle actions
* Render page or redirect

Request parameter conversion is not possible if the incoming
	value is not well-formed. For example, a value of
	"3f" cannot be converted to an integer. Since a
	failed conversion invalidates any input this triggers re-rendering the page with error messages. 

In the first phase, parameters are decoded from strings. In
	the 'Update Model Values' phase, these parameters are
	automatically inserted in data model entities. WebDSL supports
	such data binding through input elements.  For
	example, the element 

    input(u.email) 

declares that an
	input field should be displayed with the current contents of
	the email property of variable u of type
	User. Furthermore, when a user submits the containing
	form with a new value in the email field, the new value will
	be assigned to u.email. 

Data binding requires assignments to and collection
	operations on entity properties which trigger validation
	checks defined in the entity. When a property is validated
	each validation rule defined on that property is checked,
	possibly producing multiple error messages. When at least one
	validation fails during this phase, further processing is
	disabled and errors are displayed.

When the model is updated and entity validations are checked,
	there can still be validation rules in pages which need to be
	enforced. The form validation phase
	traverses the form that is submitted and checks any validation
	it encounters. An invalid result prevents any action from
	executing and produces an error in the page.

When all validation checks in previous phases have succeeded,
	the selected action is executed. During the execution of an
	action there can be action assertions that validate the data in
	the current execution state of the action.  Moreover, data
	invariants are still checked during this phase and can produce
	validation errors as well. If any validation check fails, the
	entire action is cancelled (clearing all changes made during that request).

Validation messages produced in the previous phases result in
	a re-render of the same page with error messages inserted. If
	all validations succeed, the action results in a redirect to
	the same or a different page.

Notes:

* Unless validation fails at some point, all changes made to entities are persisted, except for transient entities (new entities that weren't in the database before), these need to be explicitly saved (by calling entity.save()).
	