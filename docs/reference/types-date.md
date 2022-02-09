# Types - Date

Represents a date (not including a time). The page input for a Date is a textfield, the expected format is dd/MM/yyyy. The page output for a Date shows the Date formatted with dd/MM/yyyy. Use the format function to customize the output format.

The default value for Date properties and variables is null. Note that all Date types are DateTime at run-time.

The Date type is compatible with the DateTime and Time types. A Date can be cast to these types.

---

Date Creation Functions
----

**Date(String):Date**

Dates can be constructed using the <tt>Date</tt> constructor (expected format dd/MM/yyyy):

    var d : Date := Date("04/09/2009");

**Date(String, String):Date**

The second parameter represents the date formatting string.

    var d1 : Date := Date("12-20-1990", "MM-dd-yyyy");

**today():Date**

Creates a Date containing the current day and time 00:00.

**age(Date):Int**

Gets the age from a date of birth.

Functions
----

**format(formatstring:String):String**

Format this DateTime using the **formatstring**. See [Java SimpleDateFormat class documentation](http://download.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html) for formatstring syntax.

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

