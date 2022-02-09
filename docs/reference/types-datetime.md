# Types - DateTime

Represents both a date and a time. The page input for a DateTime is a textfield, the expected format is dd/MM/yyyy H:mm. The page output for a DateTime shows the DateTime formatted with dd/MM/yyyy H:mm. Use the format function to customize the output format.

The default value for DateTime properties and variables is null.

The DateTime type is compatible with the Time and Date types. A DateTime can be cast to these types.

---

Date Creation Functions
----

**DateTime(String):DateTime**

Dates can be constructed using the <tt>Date</tt> constructor (expected format dd/MM/yyyy H:mm):

    var dt : DateTime := DateTime("22/06/1983 22:08");

**DateTime(String, String):DateTime**

    var dt : DateTime := DateTime("12:12 05-1994-06", "mm:H MM-yyyy-dd");

The second parameter represents the date/time formatting string.

**now():DateTime**

Creates a DateTime containing the current time and day.

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

**getSecond():Int**

Gets the second.

**getMinute():Int**

Gets the minute.

**getHour():Int**

Gets the hour.

**getDay():Int**

Gets the day of the month.

**getDayOfYear():Int**

Gets the day of the year.

**getMonth():Int**

Gets the month.

**getYear():Int**

Gets the year.

