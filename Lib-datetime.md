A library for date and time.

Work in progress: [datetime-rs](https://github.com/depp/datetime-rs) (far from ready for use)

### Concepts

  * **Instant**: an infinitesimal location in the time continuum.
  * **Time Interval**: a part of the the time continuum limited by two instants.
  * **Time Scale**: a system of ordered marks of instants. One instant is chosen as the Origin, see Epoch.
  * **Epoch**: the origin of a particular era, e.g. the Incarnation of Jesus.
  * **Time Point**: an instant, defined by the means of a Time Scale.
  * **Duration**: a non-negative length of time.
  * **International Atomic Time (TAI)**: ?
  * **Coordinated Universal Time (UTC)**: is the primary time scale by which the world regulates clocks and time.
  * **Standard Time**: the result of synchronizing clocks in different geographical locations within a time zone to the same time.
  * **Local Time**: a time in the local non-UTC based standard time

See ISO 8601, which relies on IEC 60050-111, IEC 60050-713.

### Things to be aware of
  * leap seconds
  * infinity
  * high resolution
  * network time sources
  * daylight saving time
  * eras, e.g. BC/AD
  * time zone changes, sometimes on short notice

## 1. Announcement to mailing list

  - Proposed editor: Luis de Bethencourt
  - Date proposed: 12-09-2013
  - Link: https://mail.mozilla.org/pipermail/rust-dev/2013-September/005528.html

###  Notes from discussion on mailing list

  - http://noda-time.blogspot.com/2011/08/what-wrong-with-datetime-anyway.html
  - http://en.wikipedia.org/wiki/Epoch_(reference_date)
  - http://lucumr.pocoo.org/2011/7/15/eppur-si-muove/
  - http://blog.joda.org/2009/11/why-jsr-310-isn-joda-time_4941.html?m=1
  - http://naggum.no/lugm-time.html


## 2. Research of standards and techniques

### Standards
  1. [ISO 31-1](http://en.wikipedia.org/wiki/ISO_31-1) defines the units **day, hour, minute, second** for time, time interval and duration
  1. [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)days
  1. [Gregorian calendar](http://en.wikipedia.org/wiki/Gregorian_calendar)
    - 1582-10-04 last day of the Julian calendar, followed by
    - 1582-10-15 first day of the Gregorian calendar
  1. [ITU-R TF.460-5](http://www.itu.int/dms_pubrec/itu-r/rec/tf/R-REC-TF.460-5-199710-S!!PDF-E.pdf)
  1. [Coordinated Universal Time (UTC)](http://en.wikipedia.org/wiki/Coordinated_Universal_Time)
    - [Leap second](http://en.wikipedia.org/wiki/Leap_second)
  1. [Julian Day](http://en.wikipedia.org/wiki/Julian_day)
    - Epoch: -4714-11-24T12:00:00Z
  1. Modified Julian Day
    - Epoch: 1858-11-17T00:00:00Z
    - MJD = JD - 2400000.5
  1. [Unix Time](http://en.wikipedia.org/wiki/Unix_Time)
    - Epoch: 1970-01-01T00:00:00Z
    - UnixTime = (JD ??? 2440587.5) ?? 86400
  1. TAI (Temps Atomique International)
    - The underlying atomic time, non-leap seconds
    - [International Atomic Time](https://en.wikipedia.org/wiki/International_Atomic_Time)
    - [UTC vs. TAI discussion](http://cr.yp.to/proto/utctai.html)
  1. [Network Time Protocol](http://en.wikipedia.org/wiki/Network_Time_Protocol)
  1. [IANA Time Zone Database](http://www.iana.org/time-zones)
  1. [Sources for Time Zone and Daylight Saving Time Data](http://www.twinsun.com/tz/tz-link.htm)

### Techniques

  * Gregorian Date <=> Julian Day conversion:
    * Letter to the editor of Communications of the ACM (CACM, volume 11, number 10, October 1968, p.657) Henry F. Fliegel and Thomas C. Van Flandern


### Those intended to follow (and why)

### Those intended to ignore (and why)


## 3. Research of libraries from other languages

  * [Proposal to Add Date-Time to the C++ Standard Library](http://www.crystalclearsoftware.com/libraries/date_time/proposal_v75/date_time_standard.html)
  * https://github.com/ThreeTen/threeten/wiki/Multi-calendar-system

### Simple immutable objects 
Used by: Boost, Cocoa, JSR-310

### Separate date and time types

Options:
  * having separate types for Date and Time
    * Used by: Boost, Haskell, Joda, Python, Qt, Ruby
    * Cons:
      * more complex API (to use and implement)
    * Pros:
      * more space-efficient if one only needs date or time
      * more expressive (e.g. for birthdays often the time of birth is not known)
  * having only one type for Date/Time 
    * Used by: Cocoa, Go, .Net
    * Cons:
      * less space-efficient if one only needs date or time
      * less expressive (e.g. for birthdays often the time of birth is not known)
    * Pros:
      * simpler API (to use and implement)
   
### Integers vs floats

All known representations are based on the time passed from a certain epoch, either counted in days or seconds/milliseconds/nanoseconds.

Options:
  * floats
    * Used by:
      * Cocoa: NSTimeInterval := double (seconds since the Cocoa epoch)
    * Cons:
      * ?
    * Pros:
      * ?
  * integers
    * Used by:
      * Boost: int?? (template argument) (days since 1400-01-01)
      * Go: int64 (seconds since 0001-01-01) + int32 (nanoseconds)
      * Haskell: arbitrary-precision integers (days since Modified Julian Day epoch)
      * Joda: long (milliseconds since the Unix Time epoch)
      * Qt: int64 (days since the Julian Day epoch)
      * Ruby: int (days since the Julian Day epoch)
    * Cons:
      * ?
    * Pros:
      * faster comparison and calculation
      * more space-efficient
      * lossless calculations 

### Epochs

Epochs are the reference for the internal date/time counting. It does not matter so much, which instant in time one chooses if the type is large enough (Year 2038 problem for int32).

Options:
  * **0001-01-01T00:00:00Z**
    * Used by: Go
  * **Julian Day**
    * Used by: Qt
    * Cons:
      * ?
    * Pros:
      * ?
  * **Modified Julian Day**
    * Used by: Haskell
    * Cons:
      * ?
    * Pros:
      * more space-efficient
  * **Unix Time**
    * Used by: Joda, Unix
    * Cons:
      * ?
    * Pros:
      * ?
  * **2001-01-01T00:00:00Z**
    * Used by: Apple Cocoa
    * Cons:
      * ?
    * Pros:
      * ?

### Separate date/time and calendar interfaces

Options:
  * date/time interface is coupled to a calendar (Gregorian)
    * Used by: Go, Qt
    * Cons:
      * ?
    * Pros:
      * less code is needed to get started
  * date/time interface is tied to a configurable calendar
    * Used by: Boost, Joda
    * Cons:
      * ?
    * Pros:
      * not much code is needed to get started
  * date/time interface is independent of a calendar
    * Used by:
      * Cocoa: NSCalendar create NSDates and are used for calendrical calculation
    * Cons:
      * more code is needed to get started
    * Pros:
      * seperated interfaces are themselves easier to understand and extend

### Support for date/time without a timezone

Sometimes the timezone information is missing, e.g. parsing from a textual representation.

Options:
  * support Date/Time with no attached timezone
    * Used by:
      * Joda: LocalDate, LocalTime, LocalDateTime
    * Cons:
      * more complex interfaces/types
      * ?
    * Pros:
      * clear distinction between dates that are comparable and date that are not
      * more space-efficient
  * assume the local timezone if the timezone is not available
    * Used by:
      * Cocoa
    * Cons:
      * dates are created that appear to be comparable, but are not. One needs to keep tracks of those dates.
      * less space-efficient
    * Pros:
      * ?


### Reference
  1. Language: C
    - [time.h](http://pubs.opengroup.org/onlinepubs/7908799/xsh/time.h.html)
    - [glib](https://developer.gnome.org/glib/2.36/glib-Date-and-Time-Functions.html)
    - [libtai](http://cr.yp.to/libtai.html)
    - [Modernized `<time.h>` API proposal](http://www.cl.cam.ac.uk/~mgk25/time/c/) by Markus Kuhn
  1. Language: C++
    - [Boost.Date_Time](http://www.boost.org/doc/libs/1_53_0/doc/html/date_time.html)
        - boost::date_time
            - [boost::date_time::date](http://www.boost.org/doc/libs/1_53_0/doc/html/boost/date_time/date.html)
            - [boost::date_time::period] (http://www.boost.org/doc/libs/1_53_0/doc/html/boost/date_time/period.html)
        - boost::date_time::gregorian
          - [Gregorian](http://www.boost.org/doc/libs/1_53_0/doc/html/date_time/gregorian.html)
            - valid date range: 1400-01-01 to 9999-12-03
        - boost::date_time::posix_time
            - [Posix Time](http://www.boost.org/doc/libs/1_53_0/doc/html/date_time/posix_time.html)
        - [Unit tests](http://svn.boost.org/svn/boost/trunk/libs/date_time/test/)
    - Qt
        - [QDate](http://qt-project.org/doc/qt-5.0/qtcore/qdate.html)
        - [QTime](http://qt-project.org/doc/qt-5.0/qtcore/qtime.html): a clock time since midnight
        - [QDateTime](http://qt-project.org/doc/qt-5.0/qtcore/qdatetime.html)
        - Unit tests:
            - https://qt.gitorious.org/qt/qt/blobs/4.8/tests/auto/qdate/tst_qdate.cpp
            - https://qt.gitorious.org/qt/qt/blobs/4.8/tests/auto/qtime/tst_qtime.cpp
            - https://qt.gitorious.org/qt/qt/blobs/4.8/tests/auto/qdatetime/tst_qdatetime.cpp
    - QuantLib
        - [Date](http://quantlib.org/reference/class_quant_lib_1_1_date.html)
        - [Period](http://quantlib.org/reference/class_quant_lib_1_1_period.html)
        - [Calendar](http://quantlib.org/reference/class_quant_lib_1_1_calendar.html)
  1. Language: Go
    - [time](http://golang.org/pkg/time/)
        - [Duration](http://golang.org/pkg/time/#Duration): a duration with an int64 nanosecond count.
        - [Location](http://golang.org/pkg/time/#Location): maps time instants to the zone in use at that time.
        - [Time](http://golang.org/pkg/time/#Time): an instant in time with nanosecond precision.
  1. Language: Haskell
    - [Data.Time](http://www.haskell.org/ghc/docs/latest/html/libraries/time-1.4.0.1/index.html)
  1. Language: Java
    - [JSR-310](https://github.com/ThreeTen/threeten) ([new location](http://openjdk.java.net/projects/threeten/)) ([API](http://download.java.net/jdk8/docs/technotes/guides/datetime/index.html))
    - [Joda-Time](https://github.com/JodaOrg/joda-time)
        - [DateTime](http://joda-time.sourceforge.net/apidocs/org/joda/time/DateTime.html): the datetime as milliseconds from the Unix epoch and a Chronology (calendar system).
        - [Chronology](http://joda-time.sourceforge.net/apidocs/org/joda/time/chrono/ISOChronology.html)
        - [LocalDate](http://joda-time.sourceforge.net/apidocs/org/joda/time/LocalDate.html): a date without a time zone.
        - [LocalTime](http://joda-time.sourceforge.net/apidocs/org/joda/time/LocalTime.html):  a time without a time zone.
        - [LocalDateTime](http://joda-time.sourceforge.net/apidocs/org/joda/time/LocalDateTime.html): a datetime without a time zone.
    - java.util
        - [java.util.Date](http://docs.oracle.com/javase/7/docs/api/java/util/Date.html): a time period with millisecond duration
        - [java.util.Calendar](http://docs.oracle.com/javase/7/docs/api/java/util/Calendar.html)
        - [java.util.GregorianCalendar](http://docs.oracle.com/javase/7/docs/api/java/util/GregorianCalendar.html)
        - [java.util.TimeZone](http://docs.oracle.com/javase/7/docs/api/java/util/TimeZone.html)
        - [java.util.SimpleTimeZone](http://docs.oracle.com/javase/7/docs/api/java/util/SimpleTimeZone.html)
  1. Language: Objective-C
    - [Cocoa Date and Time Programming Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/DatesAndTimes/DatesAndTimes.html)
      - [NSDate](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSDate_Class/Reference/Reference.html)
      - [NSCalendar](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSCalendar_Class/Reference/NSCalendar.html)
      - [NSDateComponents](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSDateComponents_Class/Reference/Reference.html)
  1. Language: Python
    - builtin
        - [datetime](http://docs.python.org/3.3/library/datetime.html)
            - distinguishes between "naive" and "aware" date/time objects
            - [datetime.date](http://docs.python.org/3.3/library/datetime.html#datetime.date)
            - [datetime.time](http://docs.python.org/3.3/library/datetime.html#datetime.time)
            - [datetime.datetime](http://docs.python.org/3.3/library/datetime.html#datetime.datetime)
            - [datetime.timedelta](http://docs.python.org/3.3/library/datetime.html#datetime.timedelta)
            - [datetime.timezone](http://docs.python.org/3.3/library/datetime.html#datetime.timezone)
        - [time](http://docs.python.org/3.3/library/time.html)
        - [calendar](http://docs.python.org/3.3/library/calendar.html#module-calendar)
    - [pytz](http://pytz.sourceforge.net/)
    - [dateutil](http://labix.org/python-dateutil)
  1. Language: Ruby
    - [Date](http://ruby-doc.org/stdlib-2.0/libdoc/date/rdoc/Date.html)
  1. Language: Scheme
    - [SRFI 19](http://srfi.schemers.org/srfi-19/srfi-19.html)

## 4. Module writing
  - https://github.com/luisbg/rust-datetime

## 5. Use cases
   - http://sourceforge.net/apps/mediawiki/threeten/index.php?title=Use_cases