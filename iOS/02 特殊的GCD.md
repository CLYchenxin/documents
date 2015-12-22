#特殊的GCD
###1 dispath_semaphore_t

``` objective-c
/*!
 *  讲block内容变同步;注：block内执行的线程和当前线程不能是同一线程
 */
- (void)simple3 {

    dispatch_semaphore_t semaphore = dispatch_semaphore_create(0);

    NSString *urlString = @"http://www.baidu.com";
    NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSession *session  = [NSURLSession sessionWithConfiguration:config];

    NSURLSessionTask *task = [session dataTaskWithURL:[NSURL URLWithString:urlString] completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

        NSLog(@"url:%@ queue:%@", urlString, [NSThread isMainThread] ? @"main" : [NSThread currentThread].name);

        dispatch_semaphore_signal(semaphore);
    }];
    [task resume];

    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);

    NSLog(@"执行结束");

}
```
###2 dispath_group_t

``` objective-c
/*!
 *  执行多个block，并且在执行结束后执行自定义的一个block
 */
- (void)simple1 {

    NSArray *urlStrings = @[
                            @"http://www.taobao.com",
                            @"http://www.baidu.com",
                            @"http://www.qq.com",
                            @"http://www.ucai.cn",
                            @"http://www.bing.com",
                            @"http://www.jianshu.com"];

    NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSession *session  = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:[NSOperationQueue mainQueue]];

    dispatch_group_t group = dispatch_group_create();

    for (NSString *urlString in urlStrings) {

        NSURLSessionTask *task = [session dataTaskWithURL:[NSURL URLWithString:urlString] completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

            NSLog(@"url:%@ queue:%@", urlString, [NSThread isMainThread] ? @"main" : [NSThread currentThread].name);

            dispatch_group_leave(group);
        }];
        [task resume];

        dispatch_group_enter(group);
    }

    dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        NSLog(@"请求结束, %@", [NSThread isMainThread] ? @"main" : [NSThread currentThread].name);
    });

}
```
###3 dispath_barrier_async

`void dispatch_barrier_async( dispatch_queue_t queue, dispatch_block_tblock);`这个函数可以设置同步执行的block，它会等到在它加入队列之前的block执行完毕后，才开始执行。在它之后加入队列的block，则等到这个block执行完毕后才开始执行。

###4 主线程执行

``` objective-c
#define dispatch_main_sync_safe(block)\
    if ([NSThread isMainThread]) {\
        block();\
    } else {\
        dispatch_sync(dispatch_get_main_queue(), block);\
    }

#define dispatch_main_async_safe(block)\
    if ([NSThread isMainThread]) {\
        block();\
    } else {\
        dispatch_async(dispatch_get_main_queue(), block);\
    }
```


