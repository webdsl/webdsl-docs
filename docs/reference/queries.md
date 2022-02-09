# Queries

WebDSL supports a subset of [HQL](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/queryhql.html). To escape to WebDSL inside a query, prefix the expression with ~.

## Examples

```
// select all updates
var allUpdates : List<Update> := from Update;

// Select all users whose username is "zef"
var username : String := "zef";
var users : List<User> := from User as u where u.username = ~username;

// A paginated for, showing 10 items per page and prefetch the "user" column (in a template)
var page : Int := 0;
for(update : Update in from Update as u left join fetch u.user order by u.date desc limit 10*page, 10) {
   output(update.text)
}
```
