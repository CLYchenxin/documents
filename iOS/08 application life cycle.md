# application life cycle
<img src="https://raw.githubusercontent.com/CLYchenxin/documents/master/img/D4E9053D-98AC-4DC6-9F7C-98248BF21463.png" height="400">
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

