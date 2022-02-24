# Session Entities

Storing data in the session context on the server is done using session entities. Example:

    session shoppingcart {
      products : [Product]
    }

A session entity name is a globally visible variable in the application code. The entity object is automatically instantiated and saved, one for each browser session accessing the application.

Typically, session data is used for keeping track of authentication state, but it can also be used for temporarily storing data for anonymous users. A common oversight with session data is that it is shared between tabs in a browser.

To initialize session data to default values, remember that using `:=` after a field means that that field is a derived property. 
That also means you can't assign to this field. To set defaults use `(default = value)`. Example:


    session history {
      number_of_entries : Int (default = 10)
    }


Declaring an access control principle, e.g. `principal is User with credentials name,password`, automatically creates a `securityContext` session entity. For more information about access control see the [Access Control](../access-control) page.

Session entities can also be extended with extra properties. Example:

    extend session shoppingcart{
      lastSearchQuery : String
    }

Session data times out by default, this timeout length can be adjusted in the `application.ini` file, e.g. `sessiontimeout=10080`. This time is specified in minutes. More information about application settings is shown on the [Application Configuration](../app-configuration) page.
