[TOC]

> 类簇是一种在Foundation框架中广泛使用的一种设计模式。类簇中包含一个公开的虚基类，和一堆私有的子类。类簇是基于抽象工厂模式(Abstract Factory design pattern)的。

#01 相同的接口，不同的实现

在这种情况下，可以参考类`NSNumber`。Number的子类拥有许多相同的特征，如，它们之间可以相互转换，并且都可以转换成字符串。但是它们的存储的数据类型是不一样的。可以设计下面的设计来决解这个问题。

<img src="https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/Art/cluster3.gif">

图1-1 NSNumber类及其子类。

其中，Number是一个虚基类，在这个类中声明它子类需要的所有方法，但是它不存储数据。在子类中拥有数据，并且实现在父类中声明的方法。在使用这一堆类时，你只能看到唯一的公开类Number，它的子类是不公开的，那么如何创建对象呢？答案是使用虚基类来实现初始化。

在类簇的实现过程中，虚基类必须创建为它的子类创建初始化方法。对于NSNumber来说，使用类方法来初始化子类。

``` objective-c
NSNumber *aChar = [NSNumber numberWithChar:’a’];
NSNumber *anInt = [NSNumber numberWithInt:1];
NSNumber *aFloat = [NSNumber numberWithFloat:1.0];
NSNumber *aDouble = [NSNumber numberWithDouble:1.0];
```

每一个对象aChar、anInt、aFloat、aDouble是属于不同的子类，在这里它们和他们的类之间的关系隐藏，它们的接口是公开的并且相同。通常为了简化，讲这些对象看成是NSNumber的对象，因为他们通过NSNumber类来创建。

#02 Multiple Public Superclasses

在上面的例子中，是一个公开的虚基类和很多私有的子类。上面这种是一种比较理想的实现。但通常情况下，会声明两个（或更多）的虚基类，在Foundation框架中有一下几个：

* NSData 
	* NSData
	* NSMutableData
* NSArray
	* NSArray
	* NSMutableArray
* NSDictionary
	* NSDictionary
	* NSMutableDictionary
* NSString
	* NSString
	* NSMutableString

类簇中的类可以按照是否公开可以分成虚基类和子类。在上面的类簇中，它们有可以分为两部分，一部分是可以不能修改的虚基类及它的子类，另外一部分是可以修改的虚基类及它的子类。

<img src="https://raw.githubusercontent.com/CLYchenxin/documents/master/img/nsarrayandsubclass.png">

图2-1 NSArray类的组成

其中NSArray和NSMutableArray是虚基类，它们是没有对象的，通过它们创建的对象都是在类簇中隐藏的子类。

#03 创建类簇的子类

创建一个子类：

1. 创建一个类簇的虚基类的子类
2. 声明它的存储数据
3. 重写基类的所有初始化方法
4. 重写基类的所有原始方法(primitive methods)

因为类簇的虚基类只是声明了这个类簇的对外接口，并且虚基类没有定义数据，因此在子类中必须定义数据（属性）。最后，子类必须继承虚基类所有的初始化方法和原始方法。

一个类的原始方法使它最基本的接口。例如，NSArray类，这个类有两个原始方法`- count`、`- objectAtIndex:`。其他的方法如`- lastObject`、`- containsObject`，都是原始方法的延伸，这些方法称为：衍生方法(derived methods)。

将衍生方法和原始方法区分开使得继承一个类更加容易，你的子类必须继承原始方法，这么做之后，它的衍生方法将正常使用。

[例子]创建一个NSArray的子类MonthArray，给他一个数，他返回月份。当然，MonthArray没有存储月份的数组，因为这个月份在整个程序中都是一样的，它作为一个全局变量存在。

MonthArray的声明为：

``` objective-c
#import <Fooundation/Foundation.h>
@interface MonthArray: NSArray
+ (id)monthArray;
- (unsigned)count;
- (id)objectAtIndex:(unsigned)index;
@end
```

注意MonthArray没有声明`init`方法，因为它没有变量需要声明。`count`、`objectAtIndex:`重写了父类的原始方法。

MonthArray的实现为：

``` objective-c
#import "MonthArray.h"

@implementation MonthArray

static MonthArray *sharedMonthArray = nil;
static NSString *months[] = {@"January", @"February", @"March", @"April", @"May", @"June", @"July", @"August", @"September", @"October", @"November", @"December"};

+ (id)monthArray {
	if (!sharedMonthArray) {
		sharedMonthArray = [[MonthArray alloc] init];
	}
	return sharedMonthArray;
}

- (unsigned)count {
	return 12;
}

- (id)objectAtIndex:(unsigned)index {
	if (index >= [self count]) {
		[NSException raise:NSRangeException format:@"***%s: index (%d) beyond bounds (%d)", sel_getName(_cmd), index, [self count] - 1];
	} else {
		return months[index];
	}
}

@end
```

因为MonthArray继承了父类的运势方法，所以衍生方法会自动运行，`NSArray`的方法`lastObject` `containsObject:`、`sortedArrayUsingSelector:`、`objectEnumerator`，以及其它方法会运行在MonthArray中运行良好。

#04 使用复合来创建类簇的子类

[例子]创建一个可变的数组，当修改数组时，只允许受到许可的数据进行修改。在例子中声明一个ValidatingArray，它里面包含一个可变的数组。ValidatingArray会声明NSArray、NSMutabeArray中的原始方法。同时它也声明`init`方法，用来初始化里面的变量。

``` objective-c
#import <foundation/foundation.h>
 
@interface ValidatingArray : NSMutableArray
{
    NSMutableArray *embeddedArray;
}
 
+ validatingArray;
- init;
- (unsigned)count;
- objectAtIndex:(unsigned)index;
- (void)addObject:object;
- (void)replaceObjectAtIndex:(unsigned)index withObject:object;
- (void)removeLastObject;
- (void)insertObject:object atIndex:(unsigned)index;
- (void)removeObjectAtIndex:(unsigned)index;
 
@end
```

在实现文件中，显示在`init`方法中，`embeddedArray`被创建和初始化。当修改内容是，只有通过测试的才能进行修改。

``` objective-c
#import "ValidatingArray.h"
 
@implementation ValidatingArray
 
- init {
    self = [super init];
    if (self) {
        embeddedArray = [[NSMutableArray allocWithZone:[self zone]] init];
    }
    return self;
}
+ validatingArray {
    return [[[self alloc] init] autorelease];
}
 
- (unsigned)count
{
    return [embeddedArray count];
}
 
- objectAtIndex:(unsigned)index
{
    return [embeddedArray objectAtIndex:index];
}
 
- (void)addObject:object
{
    if (/* modification is valid */) {
        [embeddedArray addObject:object];
    }
}
 
- (void)replaceObjectAtIndex:(unsigned)index withObject:object;
{
    if (/* modification is valid */) {
        [embeddedArray replaceObjectAtIndex:index withObject:object];
    }
}

- (void)removeLastObject;
{
    if (/* modification is valid */) {
        [embeddedArray removeLastObject];
    }
}
- (void)insertObject:object atIndex:(unsigned)index;
{
    if (/* modification is valid */) {
        [embeddedArray insertObject:object atIndex:index];
    }
}
- (void)removeObjectAtIndex:(unsigned)index;
{
    if (/* modification is valid */) {
        [embeddedArray removeObjectAtIndex:index];
    }
}

```

***
参考资料：

1. <a href="https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/ClassClusters/ClassClusters.html#//apple_ref/doc/uid/TP40010810-CH4-SW1">Concepts in Objective-C Programming</a>

