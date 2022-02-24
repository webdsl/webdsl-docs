# Queries

WebDSL supports a subset of [HQL](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/queryhql.html). You can use these queries directly in your webdsl program, by for example assigning them to variables. An example:

      // select all updates
      var allUpdates : [Update] := from Update;

## Escaping

Often it is useful to use values from your WebDSL program inside a query. To do this you can escape WebDSL expressions by prefixing them with a tilde (`~`). 


To escape to WebDSL inside a query, prefix the expression with ~. For example:

      // Select all users whose username is "zef"
      var username : String := "zef";
      var users : [User] := from User as u where u.username = ~username;

## Notes

* Limit accepts either one or two arguments. With one argument it just limits, but in the two argument form, the first argument describes the `offset`. Even though `offset` is valid SQL, it's not a valid keyword to use in WebDSL queries, even though it is a valid keyword in other places in WebDSL so will be highlighted in your editor.


## Example: a paginated view

```
// A paginated for, showing 10 items per page and prefetch the "user" column (in a template)
var page : Int := 0;
for(update : Update in from Update as u left join fetch u.user order by u.date desc limit 10*page, 10) {
   output(update.text)
}
```
