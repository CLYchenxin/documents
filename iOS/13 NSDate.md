# 01 概述

日期(Date)和时间(time)使用来存储时间的，它们可以用来计算时间、相互比较。

在时间和日期中住要用到的类：

* `NSDate`相当于一个绝对的时间点
* `NSCalendar`相当于一个特指的历法，不同地区的历法是不一样的。（如农历和公历），可以使用这个类进行时间的计算，以及`NSDate`之间的相互转换`NSDateComponents`。
* `NSTimeZone`是一个时区的概念，如“东八区”。在不同时区内，相同的时间是有不一样的描述。
* `NSDateComponents`时间的描述，包含许多日历单元，例如年、月、日的概念。
* `NSDateFormatter`是一个简单的类，将NSDate对象转换成字符串。

NSDate是一个绝对的值，不会因位置、历法、时区的不同而不同。NSDateComponents是一个相对的值，它里面存储的是日历单元的一些属性，如年、星期等等，同时也可以设置不同的时区，是一个相对的值，更具历法的不同而不同。

# 02 NSDate

NSDate涌来描述时间和日期，并且可以应用于日期计算和转换。作为一个绝对的值，NSDate在位置、时区、历法中非常有意义。

### 2.1 简介

NSDate提供的方法包含：创建对象、比较时间对象、计算时间间隔。NSDate对象是不可变的。时间的表示单位是一个float，他有一个别名NSTimeInterval，他表示秒。他表示的最小单位是ms，对大单位是10000年。

NSDate对象使用的时间戳的相对时间是2001-1-1 CMT，这个时间之前的时间是负的，这个时间之后的时间是正的。NSDate的原始方法为`timeIntervalSinceReferenceDate`，其它所有的方法都是衍生方法。时间对象存储的就是与相对时间之间的间隔。

###2.2 初始化

使用初始化方法创建时间对象

* `date`当前的时间
* `dateWithTimeInterval`通过时间戳来创建时间

``` objective-c
NSTimeInterval secondsPerDay = 24 * 60 * 60;
NSDate *tomorrow = [[NSDate alloc] initWithTimeIntervalSinceNow:secondsPerDay];
NSDate *yesterday = [[NSDate alloc] initWithTimeIntervalSinceNow:-secondsPerDay];
[tomorrow release];
[yesterday release];
```

通过一个时间对象创建另外一个时间对象：

``` objective-c
NSTimeInterval secondsPerDay = 24 * 60 * 60;
NSDate *today = [[NSDate alloc] init];
NSDate *tomorrow, *yesterday;
 
tomorrow = [today dateByAddingTimeInterval: secondsPerDay];
yesterday = [today dateByAddingTimeInterval: -secondsPerDay];
[today release];
```

###2.3 常用方法

* 原始方法：
	* `timeIntervalSinceReferenceDate`时间对象相对于2001-1-1 CMT这个时间点的差值
* 延伸方法：
	* `isEqualToDate`比较两个时间对象是否相等
	* `compare:`比较两个时间对象
	* `laterDate:`对象比另一个时间对象大
	* `earlierDate:`对象比另一个时间对象小
	* `timeIntervalSinceDate:`对象减去另一个时间对象的差值
	* `timeIntervalSinceNow`对象相对于当前时间的间隔
	* 

［例子］date2与date2时间间隔小于60秒？

```
if (fabs([date2 timeIntervalSinceDate:date1]) < 60) ...
```

# 03 NSDateComponents

`NSDateComponents`对象保存`NSDate`一样的绝对时间，以及一系列的方法用于计算时间的年、月、星期等等。

日、工作日、周末等等信息它们在不同的历法中都是不同的，同时不同的历法需要的信息也是不同的。

［例子］创建一个NSDateComponents，他的时间是2004-5-6（公历），`weekday`必须另外说明，否则就没有值。

``` objective-c
NSDateComponents *components = [[NSDateComponents alloc] init];
[components setDay:6];
[components setMonth:5];
[components setYear:2004];
 
NSInteger weekday = [components weekday]; // Undefined (== NSUndefinedDateComponent)
```

* `era`纪元前，公元后
* `year`
* `month`
* `day`
* `hour`
* `minute`
* `second`
* `nanosecond`毫秒
* `weekday`星期，这个值1～N，在公历中N＝7，星期天为N＝1
* `weekdayOrdinal`工作日序列，一个月的第几周
* `quarter`季度
* `weekOfMonth`当月中有多少周
* `weekOfYear`当年有多少周
* `yearForWeekOfYear`ISO 8601中日历星期
* `calendar`历法
* `timeZone`时区

* `leapMonth`当前周是否是跳跃的周

> `NSCalendarUnitWeekday`的值为2，`NSCalendarUnitWeekdayOrdinal`的值为3，表示“当前月第三个周一”

### 3.2 NSDateComponents在不同历法之间相互转换

``` objective-c
NSDateComponents *comps = [[NSDateComponents alloc] init];
[comps setDay:6];
[comps setMonth:5];
[comps setYear:2004];
 
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDate *date = [gregorian dateFromComponents:comps];

NSCalendar *hebrew = [[NSCalendar alloc] initWithCalendarIdentifier:NSHebrewCalendar];
NSUInteger unitFlags = NSDayCalendarUnit | NSMonthCalendarUnit | NSYearCalendarUnit;
NSDateComponents *components = [hebrew components:unitFlags fromDate:date];
 
NSInteger day = [components day]; // 15
NSInteger month = [components month]; // 9
NSInteger year = [components year]; // 5764
```

# 04 NSTimeZone

可以使用一下方法创建并初始化一个TimeZone：

* timeZoneWithName:
* timeZoneWithAbbreviation:
* timeZoneForSecondsFromGMT:

可以通过方法`knownTimeZoneNames`来获取所有的时区。

可以通过`setDefaultTimeZone:`来设置默认的时区，让后通过`defaultTimeZone`方法获取默认的时区，使用`localTimeZone`来获取系统的时区。

NSDate在不同的时区，其表现形式是不一样的，如果你想创建一个时间独立与时区，也就是说，在每一个时区都一样，则使用NSDateComponents来存储时间。

* `isDaylightSavingTime`
* `daylightSavingTimeOffset`
* `nextDaylightSavingTimeTransition`

# 05 NSCalendar

`NSCalendar`对象决定系统时间系统的开始、长度、以及一年有多长。`NSCalendar`主要用于讲`NSDate`对象转换成年、月、日、小时、分钟、秒、星期等等。

`NSCalendar`对象包含了许多历法，比如：中国农历、日本历法等等。`NSCalendar`使用哪种历法使用操作系统决定的，你可以使用`NSLocale`来查看当前的历法。`NSCalendar`讲`NSDate`对象和`NSDateComponents`对象紧密联系起来，`NSDateComponents`提供了`NSDate`对象的一些组成部分，如年、月、日等。


###5.1 初始化

`NSCalendar`的具体内容是有`NSLocale`里面的一个常量决定的。你可以使用`currentCalendar`来获取当前的历法对象。同时也可以使用`NSLocale`对象中的`NSLocaleCalendar`。也可以使用初始化方法创建一个`NSCalendar`对象：

``` objective-c
NSCalendar *currentCalendar = [NSCalendar currentCalendar];
NSCalendar *usersCalendar = [[NSLocale currentLocale] objectForKey:NSLocaleCalendar];
NSCalendar *japaneseCalendar = [[NSCalendar alloc] initWithCalendarIdentifier:NSJapaneseCalendar];
```

其中`currentCalendar`与`usersCalendar`是相通的，尽管他们不是同一个对象。

常用的Calendar Identifiers有：

* `NSCalendarIdentifierGregorian` 公历
* `NSCalendarIdentifierChinese`中国农历

NSCalendar在创建的时候使用系统默认的NSTimeZone，可以设置NSTimeZone

###5.2 常用方法

* 将NSDate转换成NSDateComponents
	* `components:fromDate:`将NSDate转换成NSDateComponents，参考例子5－2-1
* 将NSDateComponents转换成NSDate
	* `dateFromComponents:`将NSDateComponents转换成NSDate，参考例子5－2-2，5-2-3
* `ordinalityOfUnit:inUnit:forDate:`获取一年有多少天。
* `dateByAddingComponents:toDate:options:`在已有的NSDate上添加一个NSDateComponents组成的时间数据参考例子5-2-4，5-2-5
* 时间比较
	* `- (NSDateComponents *)components:(NSCalendarUnit)unitFlags fromDate:(NSDate *)startingDate toDate:(NSDate *)resultDate options:(NSCalendarOptions)options`用来比较两个时间的日历单元，如年、月，而不是比较两个时间大小。例子5-2-6
* `- (BOOL)rangeOfUnit:(NSCalendarUnit)unit startDate:(NSDate * _Nullable *)datep interval:(NSTimeInterval *)tip forDate:(NSDate *)date`计算date这个时间的unit这个日历单元的初值，并将这个初值存放到datep中，top为datep和date在unit这个单元的差值。例如，unit为month，date2015-11-23，那么结果，datep为2015-11-0，tip中存放`23*24*60*60`，参考例子5-2-9
* `- (NSUInteger)ordinalityOfUnit:(NSCalendarUnit)smaller inUnit:(NSCalendarUnit)larger forDate:(NSDate *)date`获取一个序列，给一个时间，一个小的日历单元smaller（day），一个大的日历单元larger（week），这时返回的就是星期几？
* `minimumRangeOfUnit:`返回一个历法单元的最小范围
* `maximumRangeOfUnit:`返回一个历法单元的最大范围

[例子5-2-1]将NSDate转换成NSDateComponents
``` objective-c
NSDate *today = [NSDate date];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDateComponents *weekdayComponents = [gregorian components:(NSDayCalendarUnit | NSWeekdayCalendarUnit) fromDate:today];
NSInteger day = [weekdayComponents day];
NSInteger weekday = [weekdayComponents weekday];
```

[例子5-2-2]获取2008年8月的第一个星期一：

``` objective-c
NSDateComponents *components = [[NSDateComponents alloc] init];
[components setWeekday:2]; // Monday
[components setWeekdayOrdinal:1]; // The first Monday in the month
[components setMonth:5]; // May
[components setYear:2008];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDate *date = [gregorian dateFromComponents:components];
``` 

在这里，你设置的信息在历法中必须能够理解，例如2月30号，这些信息历法就不能够解析。

在理论上可以创建一个时间，不包含年份，例如存储生日：

[例子5-2-3]创建一个生日时间11月7号

``` objective-c
NSDateComponents *components = [[NSDateComponents alloc] init];
[components setMonth:11];
[components setDay:7];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDate *birthday = [gregorian dateFromComponents:components];
```

> note:在这里，因为没有设置年份，所以默认的年份是公元1年（不保证都是公元1年）。当你使用这个NSDate在创建一个新的NSDateComponents时，却保证不要使用year这个属性，可以使用NSDateFromatter的方法`dateFormatFromTemplate:options:locale:`方法创建一个没有年份的formatter。

[例子5-2-4]一个半小时之后

``` objective-c
NSDate *today = [[NSDate alloc] init];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
NSDateComponents *offsetComponents = [[NSDateComponents alloc] init];
[offsetComponents setHour:1];
[offsetComponents setMinute:30];
// Calculate when, according to Tom Lehrer, World War III will end
NSDate *endOfWorldWar3 = [gregorian dateByAddingComponents:offsetComponents toDate:today options:0];
```

[例子5-2-5]获取这个周的星期天

```objective-c
NSDate *today = [[NSDate alloc] init];
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
 
NSDateComponents *weekdayComponents = [gregorian components:NSWeekdayCalendarUnit fromDate:today];

NSDateComponents *componentsToSubtract = [[NSDateComponents alloc] init];
[componentsToSubtract setDay: 0 - ([weekdayComponents weekday] - 1)];

NSDate *beginningOfWeek = [gregorian dateByAddingComponents:componentsToSubtract toDate:today options:0];

NSDateComponents *components = [gregorian components:(NSYearCalendarUnit | NSMonthCalendarUnit | NSDayCalendarUnit) fromDate: beginningOfWeek];
beginningOfWeek = [gregorian dateFromComponents:components];
```

也可以写成

``` objective-c
NSDate *today = [[NSDate alloc] init];
NSDate *beginningOfWeek = nil;
BOOL ok = [gregorian rangeOfUnit:NSWeekCalendarUnit startDate:&beginningOfWeek interval:NULL forDate: today];
```

[例子5-2-6]比较两个时间的年、月是否相等

``` objective-c
NSDate *startDate = ...;
NSDate *endDate = ...;

NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];
 
NSUInteger unitFlags = NSMonthCalendarUnit | NSDayCalendarUnit;
NSDateComponents *components = [gregorian components:unitFlags fromDate:startDate toDate:endDate options:0];
NSInteger months = [components month];
NSInteger days = [components day];
```

[例子5-2-7]计算两个时间之间的天数间隔

``` objective-c
@implementation NSCalendar (MySpecialCalculations)
-(NSInteger)daysWithinEraFromDate:(NSDate *) startDate toDate:(NSDate *) endDate
{
     NSInteger startDay=[self ordinalityOfUnit:NSDayCalendarUnit inUnit: NSEraCalendarUnit forDate:startDate];
     NSInteger endDay=[self ordinalityOfUnit:NSDayCalendarUnit inUnit: NSEraCalendarUnit forDate:endDate];
     return endDay-startDay;
}
@end
```

上面的例子必须工作在相同的纪元，也就是说两个时间要么都是公元前，要么都是公元后。如果需要跨公元计算，则使用下面的方法

[例子5-2-8]

``` objective-c
@implementation NSCalendar (MyOtherMethod)
-(NSInteger) daysFromDate:(NSDate *) startDate toDate:(NSDate *) endDate
{
     NSCalendarUnit units=NSEraCalendarUnit | NSYearCalendarUnit | NSMonthCalendarUnit | NSDayCalendarUnit;
     NSDateComponents *comp1=[self components:units fromDate:startDate];
     NSDateComponents *comp2=[self components:units fromDate endDate];
     [comp1 setHour:12];
     [comp2 setHour:12];
     NSDate *date1=[self dateFromComponents: comp1];
     NSDate *date2=[self dateFromComponents: comp2];
     return [[self components:NSDayCalendarUnit fromDate:date1 toDate:date2 options:0] day];
}
@end
```

[5-2-9]测试一个时间是否在当前的这周内

``` objective-c
-(BOOL)isDateThisWeek:(NSDate *)date {
     NSDate *start;
     NSTimeInterval extends;
     NSCalendar *cal=[NSCalendar autoupdatingCurrentCalendar];
     NSDate *today=[NSDate date];
     BOOL success= [cal rangeOfUnit:NSWeekCalendarUnit startDate:&start interval: &extends forDate:today];
     if(!success)return NO;
     NSTimeInterval dateInSecs = [date timeIntervalSinceReferenceDate];
     NSTimeInterval dayStartInSecs= [start timeIntervalSinceReferenceDate];
     if(dateInSecs > dayStartInSecs && dateInSecs < (dayStartInSecs+extends)){
          return YES;
     } else {
          return NO;
     }
}
```

# 06 NSDateFormatters

NSDateFormatters有两个重要的方法`dateFormString:`和`stringFromDate:`来处理格式化时间，同时也可以使用方法`getObjectValue:forString:range:error:`来获取精细化的设置。

### 6.1 NSDateFormatters风格

使用NSDateFormatters是可以设置Locale，需要注意系统的Locale改变时，会发出通知`NSCurrentLocaleDidChangeNotification`

NSDateFormatters提供了一些默认的风格用来格式化NSDate：

* `NSDateFormatterNoStyle`不显示所有的日历单元
* `NSDateFormatterShortStyle`
* `NSDateFormatterMediumStyle`
* `NSDateFormatterLongStyle`
* `NSDateFormatterFullStyle`

可以使用下面方法设置风格

* `setDateStyle:`
* `setTimeStyle:`

``` objective-c
NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
[dateFormatter setDateStyle:NSDateFormatterMediumStyle];
[dateFormatter setTimeStyle:NSDateFormatterNoStyle];
 
NSDate *date = [NSDate dateWithTimeIntervalSinceReferenceDate:162000];
 
NSString *formattedDateString = [dateFormatter stringFromDate:date];
NSLog(@"formattedDateString: %@", formattedDateString);
// Output for locale en_US: "formattedDateString: Jan 2, 2001".
```

###6.2 使用自定义格式化

使用`setDateFormat:`来设置自定义的格式化风格，

``` objective-c
NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
[dateFormatter setDateFormat:@"yyyy-MM-dd 'at' HH:mm"];
 
NSDate *date = [NSDate dateWithTimeIntervalSinceReferenceDate:162000];
 
NSString *formattedDateString = [dateFormatter stringFromDate:date];
NSLog(@"formattedDateString: %@", formattedDateString);
// For US English, the output may be:
// formattedDateString: 2001-01-02 at 13:00
```

> 
1. 应该使用”yyyy“而不是“YYYY”，因为“yyyy”使用的是日历的年，而“YYYY”使用的是“ISO year－weak”日历的年。在大多数情况下“yyyy”和“YYYY”的结果是一样的，有时会不一样
2. 现在显示的是“13:00”，如果在iOS中用户设置使用12小时制，则会显示“1:00 pm”

可以使用`dateFormatFromTemplate:options:locale:`来设置模版，在不同设置中显示不同的内容。

``` objective-c
NSLocale *usLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US"];
NSString *usFormatString = [NSDateFormatter dateFormatFromTemplate:@"EdMMM" options:0 locale:usLocale];
NSLog(@"usFormatterString: %@", usFormatString);
// Output: usFormatterString: EEE, MMM d.

NSLocale *gbLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_GB"];
NSString *gbFormatString = [NSDateFormatter dateFormatFromTemplate:@"EdMMM" options:0 locale:gbLocale];
NSLog(@"gbFormatterString: %@", gbFormatString);
// Output: gbFormatterString: EEE d MMM.
```

###6.3 解析时间字符串

解析时间字符串需要使用方法：`dateFromString:`或者`getObjectValue:forString:range:error:`。

`getObjectValue:forString:range:error:`方法允许你设置需要解析的字符串的范围，并且返回实际解析的字符串范围。

``` objective-c
- (NSString *)userVisibleDateTimeStringForRFC3339DateTimeString:(NSString *)rfc3339DateTimeString {
    /*
      Returns a user-visible date time string that corresponds to the specified
      RFC 3339 date time string. Note that this does not handle all possible
      RFC 3339 date time strings, just one of the most common styles.
     */
    NSDateFormatter *rfc3339DateFormatter = [[NSDateFormatter alloc] init];
    NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
 
    [rfc3339DateFormatter setLocale:enUSPOSIXLocale];
    [rfc3339DateFormatter setDateFormat:@"yyyy'-'MM'-'dd'T'HH':'mm':'ss'Z'"];
    [rfc3339DateFormatter setTimeZone:[NSTimeZone timeZoneForSecondsFromGMT:0]];
 
    // Convert the RFC 3339 date time string to an NSDate.
    NSDate *date = [rfc3339DateFormatter dateFromString:rfc3339DateTimeString];
 
    NSString *userVisibleDateTimeString;
    if (date != nil) {
        // Convert the date object to a user-visible date string.
        NSDateFormatter *userVisibleDateFormatter = [[NSDateFormatter alloc] init];
        assert(userVisibleDateFormatter != nil);
 
        [userVisibleDateFormatter setDateStyle:NSDateFormatterShortStyle];
        [userVisibleDateFormatter setTimeStyle:NSDateFormatterShortStyle];
 
        userVisibleDateTimeString = [userVisibleDateFormatter stringFromDate:date];
    }
    return userVisibleDateTimeString;
}
```

参考资料

1. <a href="https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DatesAndTimes/DatesAndTimes.html#//apple_ref/doc/uid/10000039i">Date and Time Programming Guide</a>;

2. <a href="https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/DataFormatting.html#//apple_ref/doc/uid/10000029i">Data Formatting Guide</a>
	

