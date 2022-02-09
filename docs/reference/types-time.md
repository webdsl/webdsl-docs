# Types - Time

Represents a time (not including a date). The page input for a Time is a textfield, the expected format is H:mm. The page output for a Time shows the Time formatted with H:mm. Use the format function to customize the output format.

The default value for Time properties and variables is null. Note that all Date types are DateTime at run-time.

The Time type is compatible with the DateTime and Date types. A Time can be cast to these types.

---

Time Creation Functions
----

**Time(String):Time**

Time can be constructed using the <tt>Time</tt> function (expected format H:mm):

    var t : Time := Time("22:08");

**Time(String, String):Time**

The second parameter represents the date formatting string.

    var t1 : Time := Time("59:08", "mm:H");

Functions
----

**format(formatstring:String):String**

Format this DateTime using the **formatstring**. See [Java SimpleDateFormat class documentation](http://www.j2ee.me/javase/6/docs/api/java/text/SimpleDateFormat.html) for formatstring syntax.

**before(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is before the **arg** date and time.

**after(arg:Date/Time/DateTime):Bool**

Tests whether this date and time is after the **arg** date and time.

**addSeconds(amount:Int)**

Adds seconds, `amount` may be negative.

**addMinutes(amount:Int)**

Adds minutes, `amount` may be negative.

**addHours(amount:Int)**

Adds hours, `amount` may be negative.

**addDays(amount:Int)**

Adds days, `amount` may be negative.

**addMonths(amount:Int)**

Adds months, `amount` may be negative.

**addYears(amount:Int)**

Adds years, `amount` may be negative.

