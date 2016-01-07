#01 application life cycle
<img src="https://raw.githubusercontent.com/CLYchenxin/documents/master/img/D4E9053D-98AC-4DC6-9F7C-98248BF21463.png" width="700">
***
inactive:Run your code,but no UI events.<br>
active:Run your code, receiving and processing UI events.<br>
background:Running your code for a limited time,no UI events.<br>
suspended:your code not running.you could be killed.<br>
***
not running -> inactive<br>
`application:didFinishLaunchingWithOptions:`<br>
`UIApplicationDidFinishLaunchingNotification`.<br>
options传入内容，说明app为什么启动。<br>
例如：<br>
点击App图标，打开App<br>
其他app打开一个URL(`UIApplication`调用`openURL`)。<br>
手机进入一个特殊的区域。<br>
本地通知或远程通知点击进入。<br>
***
active -> inactive<br>
`applicationWillResignActive:`<br>
`UIApplicationWillResignActiveNotification`.<br>
这里会中断UI。<br>
例如：<br>
使用app时，一个电话过来。<br>
app人为进入后台（点击Home健）。<br>
***
inactive -> active<br>
`applicationDidBecomeActive:`<br>
`UIApplicationDidBecomeActiveNotification.`<br>
这里会继续显示UI内容。<br>
***
inactive -> background<br>
`applicationDidEnterBackground:`<br>
`UIApplicationDidEnterBackgroundNotification.`<br>
执行这个方法后，大约有5秒的时间来做任务并且返回，如果需要更多的时间使用方法UIApplication的方法`beginBackgroundTaskWithExpirationHandler:`执行结束后在block中调用`endBackgroundTask:`.<br>
***
background -> inactive<br>
`applicationWillEnterForeground:`<br>
`UIApplicationWillEnterForegroundNotification.`<br>
例子：<br>
在App运行期间点击Home健，流程为：
`active -> inactive -> background`<br>

***
在首页应用菜单页面点击App图标，流程为：<br>
应用在后台没有被杀死：`background -> inactive -> active`<br>
应用在后台已经被杀死或首次启动：`not running -> inactive -> active`<br>

***
airDrop例子：<br>
在App运行期间，使用AirDrop往手机中传输文件，会调用`ApplicationDelegate`中的方法`openURL:`。<br>
这个例子实现步骤：<br>

1. 在info.plist文件中添加 *__Document Types__* 和 *__Imported UTLs__* 。<br>
2. 在`ApplicationDelegate`中实现方法`openURL:`，当手机接受到文件时，会调用这个方法并传入文件的在手机中的地址。<br>
3. 在`openURL`方法中发起通知，在想要处理的地方接受通知。<br>

#02 Launching an app to open a URL

系统应用的URL Scheme相见<a href="https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007899">apple URL Scheme Reference</a>

###自定义URL Scheme：

在Info.plist文件中添加CFBundleURLType这个键，CFBundleURLType键对应的值为__字典数组(array of dictionaries)__，在字典中有两个属性：CFBundleURLName、CFBundleURLSchemes。

* `CFBundleURLName`类型为字符串，是URL Scheme的名字，必须保证唯一，因此推荐使用域名倒叙，如：`com.baidu`。
* `CFBundleURLSchemes`类型为字符串数组(array of strings)，包含一些URL Scheme名字，如http,mailto,tel,sms。

> 如果当前手机有两个应用使用同一个URL scheme，则在跳转APP时不会发生任何事件，因为APP不知道往哪跳转。

###URL 跳转到其他APP

跳转到其他APP使用的方式是：

``` objective-c
NSURL *myURL = [NSURL URLWithString:@"todolist://www.acme.com?Quarterly%20Report#200806231300"];
[[UIApplication sharedApplication] openURL:myURL];
```

###在另一个APP中处理URL Request。

当一个URL Request到来时，你的应用没有运行，则首先你的APP将在前台运行。这是会调用`application:willFinishLaunchingWithOptions:`或`application:didFinishLaunchingWithOptions:`在options字典中可以获取到URL，并且这个方法决定是否处理这个跳转请求，返回YES表示处理跳转请求，然后会调用方法`application:openURL:sourceApplication:annotation:`(iOS9以上调用`application:handleOpenURL:`)，在这个方法中处理跳转的请求。

<img src="https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_open_url_2x.png" width = "700"/>

图2 Launching an app to open a URL

当一个URL Request到来时，你的应用处于阻塞或后台状态，它会先移动到前台。这是，应用会调用`application:openURL:sourceApplication:annotation:`来检查URL和打开它。

<img src="https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_bg_open_url_2x.png" width = "700"/>

图3 waking a background app to open a URL

参考资料：

1. <a href="https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html#//apple_ref/doc/uid/TP40007072-CH6-SW1">App Programming Guide for iOS</a>
2. <a href=""></a>




