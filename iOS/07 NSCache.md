# NSCache
###1 bug
NSCache在收到 UIApplicationDidReceiveMemoryWarningNotification通知时，不会清除里面的数据，解决办法

``` object-c
@interface AutoPurgeCache : NSCache
@end

@implementation AutoPurgeCache

- (id)init
{
    self = [super init];
    if (self) {
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(removeAllObjects) name:UIApplicationDidReceiveMemoryWarningNotification object:nil];
    }
    return self;
}

- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self name:UIApplicationDidReceiveMemoryWarningNotification object:nil];
}

@end
```

###2 方法

``` object-c
下面这个例子，cache中不会多余10个数据
    self.cache = [[NSCache alloc] init];
    self.cache.delegate = self;
    self.cache.totalCostLimit = 10*1024*1024;
    [self.cache setObject:@(1) forKey:@(2) cost:1024*1024];

```

