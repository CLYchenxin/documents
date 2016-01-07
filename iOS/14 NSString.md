本文主要介绍如何创建、搜索、串联以及绘制NSString，同样介绍了character sets，这个主要用于在一个字符串中搜索、扫描一些字符，并且转换一些数字称字符串。

# 01 strings

介绍cocoa中独特的字符串。

一个string object实际上是一个unicode chatacters的数组。string object可以与C的字符串char *相互转换。

因为string object实际上是一个unicode chatacters的数组，所以它提供`length`方法来返回字符个数，以及`chatacterAnIndex:`来取回一个确定的字符。这两个方法都是string object的原始方法，其它所有方法都是基于这两个方法创建的。

大多数情况下，string object使用时当成一个整体，你可以比较两个字符串，使用子字符串来搜索，连接两个字符串等等。

# 02 creating and converting string objects

创建NSString、NSMutableString对象，与不同格式的字符相互转换。

NSString、NSMutableString提供了很多方法来创建一个字符串对象，并且支持很多的格式。字符串对象里面是unicode chatacters，所以可以将字符串对象与其他格式的字符相互转换，例如7-bit ASCII，ISO Latin 1，EUC以及Shift－JIS。`availableStringEncodings`这个方法返回字符串支持的格式。

你可以在C的字符串和字符串对象之间使用一个明确的格式来相互转换，或者使用C默认的格式，`defaultCStringEncoding`这个格式。

###2.1 创建字符串

最简单的创建字符串的方法是使用`@"..."`,例如

```objective-c
NSString *temp = @"Contrafibularity";
```

当使用这种方式创建一个字符串对象时，必须使用UTF-8编码。同时也可以放入Unicode数据。同时也可以插入`\u`、`\U`这些不用显示的字符串。

字符串常量在编译期间就创建了，并且不会销毁，它存储在一个特殊的内存中，同时也可以给它发送消息，如：

``` objective-c
BOOL same = [@"comparsion" isEqualToString:myString];
```

###2.2 通过C语言字符串或者NSData创建字符串对象

使用C语言字符串创建一个字符串对象可以使用`initWithCString:encoding:`方法。编码方式必须正确说明。`initWithData:encoding:`方法允许你讲NSData转换成NSString对象。

``` objective-c
char *utf8String = /* Assume this exists. */ ;
NSString *stringFromUTFString = [[NSString alloc] initWithUTF8String:utf8String];
 
char *macOSRomanEncodedString = /* assume this exists */ ;
NSString *stringFromMORString = [[NSString alloc] initWithCString:macOSRomanEncodedString encoding:NSMacOSRomanStringEncoding];
 
NSData *shiftJISData =  /* assume this exists */ ;
NSString *stringFromShiftJISData = [[NSString alloc] initWithData:shiftJISData encoding:NSShiftJISStringEncoding];
```

下面的例子讲NSString对象从UTF-8格式转换成ASCII格式的数据：

``` objective-c
unichar ellipsis = 0x2026;
NSString *theString = [NSString stringWithFormat:@"To be continued%C", ellipsis];

NSData *asciiData = [theString dataUsingEncoding:NSASCIIStringEncoding allowLossyConversion:YES];
 
NSString *asciiString = [[NSString alloc] initWithData:asciiData encoding:NSASCIIStringEncoding];

NSLog(@"Original: %@ (length %d)", theString, [theString length]);
NSLog(@"Converted: %@ (length %d)", asciiString, [asciiString length]);

// output:
// Original: To be continued… (length 16)
// Converted: To be continued... (length 18)
```

###2.3 合并和拆分字符串对象

合并字符串使用方法`stringByAppendingString:`，例如：

``` objective-c
NSString *beginning = @"beginning";
NSString *alphaAndOmega = [beginning stringByAppendingString:@" and end"];
// alphaAndOmega is @"beginning and end"
```

也可以使用初始化方法`initWithFormat:``stringWithFormat:`方法来合并字符串。

拆分字符串可以使用的方法有：`substringToIndex:``substringFromIndex:``substringWithRang:`，还可以使用方法`componentsSeparatedByString:`将字符串通过另一个字符串分割成几个字符串。

``` objective-c
NSString *source = @"0123456789";
NSString *firstFour = [source substringToIndex:4];
// firstFour is @"0123"
 
NSString *allButFirstThree = [source substringFromIndex:3];
// allButFirstThree is @"3456789"
 
NSRange twoToSixRange = NSMakeRange(2, 4);
NSString *twoToSix = [source substringWithRange:twoToSixRange];
// twoToSix is @"2345"
 
NSArray *split = [source componentsSeparatedByString:@"45"];
// split contains { @"0123", @"6789" }
```

更多的字符串拆分和合并操作可以使用NSSCanners。

###2.4 转换成C字符串

可以使用`UTF8String`方法获得C字符串，它返回`const char *`的字符串，还可以使用`dataUsingEncoding:`方法获取NSData对象，然后NSData对象转换成C的字符串。

# 03 formatting string objects

如何格式化字符串

###3.1 如何使用不在ASCII表中的字符。

可以使用方法`stringWithFormat:``stringWithUTF8String:`方法来添加特殊字符，如下：

``` objective-c
NSString *s = [NSString stringWithFormat:@"Long %C dash", 0x2014];
NSString *s = [NSString stringWithUTF8String:"Long \xe2\x80\x94   dash"];
```

#04 reading strings form and writing strings to files and urls

从文件中读取字符串以及将字符串写入文件中。

###4.1 从文件中读取字符串

使用方法`stringWithContentsOfFile:encoding:error:``stringWithContentsOfURL:encoding:error`。例如：

``` objective-c
NSURL *URL = ...;
NSError *error;
NSString *stringFromFileAtURL = [[NSString alloc] initWithContentsOfURL:URL encoding:NSUTF8StringEncoding error:&error];
if (stringFromFileAtURL == nil) {
    // an error occurred
    NSLog(@"Error reading file at %@\n%@", URL, [error localizedFailureReason]);
    // implementation continues ...
```

``` objective-c
NSURL *URL = ...;
NSData *data = [NSData dataWithContentsOfURL:URL];
 
// Assuming data is in UTF8.
NSString *string = [NSString stringWithUTF8String:[data bytes]];
 
// if data is in another encoding, for example ISO-8859-1
NSString *string = [[NSString alloc] initWithData:data encoding: NSISOLatin1StringEncoding];
```

###4.2 写入文件

使用方法`writeToFile:atomically:encoding:error:``writeToURL:atomically:encoding:error:`，必须指明写入时的编码方式。atomically如果是YES，则先讲数据写入一个临时的文件中，写入完成后将文件移动到指定的文件路径，如果是NO，则之间在制定的文件路径中写入，当文件路径中已经存在文件，则有可能报错。

# 05 searching,comparing,and sorting strings

介绍一些方法用于，查找一些字符或者子字符串，比较两个字符串。

###5.1 查询和比较

####5.1.1 查询的方法有：

* `rangeOfString:`
* `rangeOfString:options:`
* `rangeOfString:options:range:`
* `rangeOfString:options:range:locale:`
* `rangeOfCharacterFromSet:`
* `rangeOfCharacterFromSet:options:`
* `rangeOfCharacterFromSet:options:range:`

`rangeOfString:`查询一个子字符串对象，`rangeOfCharacterFromSet:`查询一些字符。

####5.1.2 比较的方法有：

* `compare:`
* `compare:options:`
* `compare:options:range:`
* `compare:options:range:locale:`

还有一些特定的方法

* `localizedCompare:`
* `caseInsensitiveCompare:`
* `localizedCaseInsensitiveCompare:`

####5.1.3 options

* `NSCaseInsensitiveSearch`忽略大小写
* `NSLiteralSearch`区分大小写比较
* `NSBackwardsSearch`从后往前比较
* `NSAnchoredSearch`仅对开始部分进行比较，如果同时选中了NSBackwardsSearch，则对结束部分进行比较。如果在开始或者结束的地方没有找到，那就返回没有找到，即使在中间部分有匹配的字符串。
* `NSNumericSearch`对字符串中的数字进行比较，如：`"Filename9.txt" < "Filename20.txt" < "Filename100.txt".`

####5.1.4 例子

NSString提供了方法`hasPerfix:``hasSuffix:`用来一个确定的前缀，后缀。下面的例子说明前面的两个方法如何实现：

``` objective-c
NSString *searchString = @"age";
 
NSString *beginsTest = @"Agencies";
NSRange prefixRange = [beginsTest rangeOfString:searchString options:(NSAnchoredSearch | NSCaseInsensitiveSearch)];
 
// prefixRange = {0, 3}

NSString *endsTest = @"BRICOLAGE";
NSRange suffixRange = [endsTest rangeOfString:searchString
    options:(NSAnchoredSearch | NSCaseInsensitiveSearch | NSBackwardsSearch)];
 
// suffixRange = {6, 3}
```

下面几个例子介绍字符串比较

``` objective-c
NSString *string1 = @"string1";
NSString *string2 = @"string2";
NSComparisonResult result;
result = [string1 compare:string2];
// result = -1 (NSOrderedAscending)
```

使用NSNumbericSearch：

``` objective-c
NSString *string10 = @"string10";
NSString *string2 = @"string2";
NSComparisonResult result;
 
result = [string10 compare:string2];
// result = -1 (NSOrderedAscending)
 
result = [string10 compare:string2 options:NSNumericSearch];
// result = 1 (NSOrderedDescending)
```

使用`caseInsensitiveCompare:` 和 `localizedCaseInsensitiveCompare:`

``` objective-c
NSString *string_a = @"Aardvark";
NSString *string_A = @"AARDVARK";
 
result = [string_a compare:string_A];
// result = 1 (NSOrderedDescending)
 
result = [string_a caseInsensitiveCompare:string_A];
// result = 0 (NSOrderedSame)
// equivalent to [string_a compare:string_A options:NSCaseInsensitiveSearch]
``` 

当需要按照列表或者表格的方式显示的字符串进行排序时，使用`localizedStandardCompare:`这个方法，类似于Finder，这个方法的排序结果在不同的地区结果是不一样的。

``` objective-c
int finderSortWithLocale(id string1, id string2, void *locale)
{
    static NSStringCompareOptions comparisonOptions =
        NSCaseInsensitiveSearch | NSNumericSearch |
        NSWidthInsensitiveSearch | NSForcedOrderingSearch;
 
    NSRange string1Range = NSMakeRange(0, [string1 length]);
 
    return [string1 compare:string2
                    options:comparisonOptions
                    range:string1Range
                    locale:(NSLocale *)locale];
}

NSArray *stringsArray = @[@"string 1",
                          @"String 21",
                          @"string 12",
                          @"String 11",
                          @"String 02"];
 
NSArray *sortedArray = [stringsArray sortedArrayUsingFunction:finderSortWithLocale
                                     context:[NSLocale currentLocale]];
 
// sortedArray contains { "string 1", "String 02", "String 11", "string 12", "String 21" }
```

#06 words,paragraphs,and line breaks

介绍字符、段落、换行的作用，如何使用段落区分一个字符串。

历史上，使用`\n`,`\r`来表示换行、段落。在unicode中使用U+2029(NSParagraphSeparatorChatacter)来表示段落，U+2028(NSLineSeparatorCharacter)表示换行。

换行和段落大部分情况下没有区别。在cocoa中`\n``\r`均视为段落标记。NSLineSeparatorCharacter相当于HTML下面的`<br>`。

除非特殊说明，一般使用段落（NSParagraphSeparatorChatacter）而不是换行（NSLineSeparatorCharacter）。cocoa默认使用`\n` 表示NSParagraphSeparatorChatacter。

将字符串按照换行区分开。

``` objective-c
NSArray *arr = [myString componentsSeparatedByString:@"\n"];
```

这时，忽略了`\r`，以及unicode separators。因此可以使用`enumerateSubstringsInRange:options:usingBlock:`和`enumerateLinesUsingBlock:`这两个方法。例如：

``` objective-c
NSString *string = /* assume this exists */;
NSRange range = NSMakeRange(0, string.length);
[string enumerateSubstringsInRange:range
                           options:NSStringEnumerationByParagraphs
                        usingBlock:^(NSString * _Nullable paragraph, NSRange paragraphRange, NSRange enclosingRange, BOOL * _Nonnull stop) {             // ... 
}];
```

#07 chatacter sets

NSCharacterSet是一个存放unicode的集合。NSCharacterSet没有方法，它作为参数被使用在NSString和NSScanner中。

###7.1 NSCharacterSet的创建

使用类方法创建一个不可变的，已经定义好的对象：

	NSCharacterSet *characterSet = [NSCharacterSet uppercaseLetterCharacterSet];

通过一个NSString创建一个自定义的对象：

	NSCharacterSet *characterSet = [NSCharacterSet characterSetWithCharactersInString:string];

###7.2 性能

由于NSCharacterSet经常参与循环等操作，因此NSCharacterSet对于性能有很大的要求，使用时。

# scanners

介绍NSScanner类，用来实现NSString的逐字扫描。

``` objective-c
NSString *string = @"Product: Acme Potato Peeler; Cost: 0.98 73\n\
Product: Chef Pierre Pasta Fork; Cost: 0.75 19\n\
Product: Chef Pierre Colander; Cost: 1.27 2\n";
 
NSCharacterSet *semicolonSet;
NSScanner *theScanner;
 
NSString *PRODUCT = @"Product:";
NSString *COST = @"Cost:";
 
NSString *productName;
float productCost;
NSInteger productSold;
 
semicolonSet = [NSCharacterSet characterSetWithCharactersInString:@";"];
theScanner = [NSScanner scannerWithString:string];
 
while ([theScanner isAtEnd] == NO)
{
    if ([theScanner scanString:PRODUCT intoString:NULL] &&
        [theScanner scanUpToCharactersFromSet:semicolonSet
            intoString:&productName] &&
        [theScanner scanString:@";" intoString:NULL] &&
        [theScanner scanString:COST intoString:NULL] &&
        [theScanner scanFloat:&productCost] &&
        [theScanner scanInteger:&productSold])
    {
        NSLog(@"Sales of %@: $%1.2f", productName, productCost * productSold);
    }
}
```

# string representations of file paths

介绍NSString的一些与文件路径相关的操作。

# drawing strins

介绍NSString的一些方法，用来讲NSString绘制到NSView中。


参考资料：

1. <a href="">String Programming Guide</a>

