# Recurring Tasks

Recurring task allow you to execute a certain function in set interval, e.g. every minute, 5 hours or every week. For this WebDSL uses the following syntax (which is subject to change):
```
function someFunction() {
  log("I was executed!");
}

invoke someFunction() every 5 minutes
```
If the called function returns anything, this value is discarded. Functions invoked in this manner have access to entities and global variables, but not session data (because the function is not invoked by a user).

Syntax of the time intervals:
```
TimeInterval = TimeIntervalPart*
TimeIntervalPart = Exp "weeks"
TimeIntervalPart = Exp "days"
TimeIntervalPart = Exp "hours"
TimeIntervalPart = Exp "minutes"
TimeIntervalPart = Exp "seconds"
TimeIntervalPart = Exp "milliseconds"
```

So valid time intervals are:
```
1 hours // note the plural
1 hours 10 minutes // repeat every 70 minutes
2 weeks 10 milliseconds
```
