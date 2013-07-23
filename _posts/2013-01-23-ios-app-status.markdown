---
author: admin
comments: true
date: 2013-01-23 06:19:02+00:00
layout: post
slug: ios%e7%a8%8b%e5%ba%8f%e7%9a%84%e5%90%8e%e5%8f%b0%e5%94%a4%e9%86%92%e5%92%8c%e8%bf%90%e8%a1%8c
title: IOS程序的后台唤醒和运行
wordpress_id: 122
categories:
- ios
---

# App状态和状态转换




## iOS app的五种状态


**非运行（Not running）**：程序未运行，或者已经运行结束并被系统终止

**非激活（Inactive）**：程序正在前台运行，但是无法进行任何事件响应。通常app只在状态转换时短暂处于这种状态（比如从后台切到前台的几秒钟内）。

**激活 （Active）**：程序正在前台运行，并处理事件响应。也就是一般程序的正常运行态。

**后台运行（Background）**：程序从前台切换到后台的短暂时间内，可以在后台继续运行一段时间，然后被挂起。当然，程序也可以请求一段额外的时间保持后台运行状态（特殊情况下可以一直保持后台运行）。另外，程序还能够一启动即进入后台运行状态，这时，用户不知道程序已经在运行（见下文）。

**挂起（Suspended）**：程序还在后台，但不能执行任何代码，并随时可能被系统结束。由于程序还在内存中，因此可以立即被切换到前台并变为激活状态。当系统内存紧张时，挂起的程序会被结束以释放内存资源供正在运行的程序使用。




## 状态转换


图1显示了一个iOS程序的状态转换。（注:iOS4.0以前没有后台运行和挂起状态，home键之后进程将被终止。另外，某些设备即使在4.0之后也没有这两种状态）


![app_status](/images/ios_app_status/app_status.jpeg)


图1 iOS程序状态转换




图中我们可以看到：

当程序未在后台，我们按下程序icon，状态变化是： 未启动 -> 非激活 -> 激活

当程序被启动过但已被home到后台，按下程序icon状态变化为：（挂起) -> 后台运行 -> 非激活 -> 激活

当我们将active的程序home到后台，状态变化是：激活 -> 非激活 -> 后台运行 -> 挂起（不能后台运行或运行时间到） -> 未运行（系统因内存警告将进程终止）




# App的后台运行




## 检测是否支持后台运行


即使app是为iOS4.0之后的设备开发，当app准备进入后台运行时，也应当检测当前设备是否有这种能力。

    
    Device* device = [UIDevice currentDevice];
    BOOL backgroundSupported = NO;
    if ([device respondsToSelector:@selector(isMultitaskingSupported)])
       backgroundSupported = device.multitaskingSupported;





## 后台运行


官方例子中的后台运行代码是这样的：

    
    - (void)applicationDidEnterBackground:(UIApplication *)application
    {
        bgTask = [application beginBackgroundTaskWithExpirationHandler:^{
            //时间到，准备被干掉（挂起），还有什么遗言要交代的
            [application endBackgroundTask:bgTask];
            bgTask = UIBackgroundTaskInvalid;
        }];
    
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)
    , ^{
           //这里开始在后台执行任务，任务完成后调用endBackgroundTask:来停止后台运行
           //任务结束，停止后台运行
            [application endBackgroundTask:bgTask];
            bgTask = UIBackgroundTaskInvalid;
        });
    }


这里，启动后台运行的方法是beginBackgroundTaskWithExpirationHandler：,

当允许的运行时间到了，或者需要执行的任务完成了，需要调用endBackgroundTask来停止。

剩余的运行时间可通过UIApplication的backgroundTimeRemaining来查询。



官方文档指出，满足以下条件之一的，app可以长时间在后台运行：

1.需要在后台播放声音的（比如各种音乐播放器）

2.需要持续获取位置信息的（比如导行）

3.需要支持VoIP的

4.需要下载并处理Newsstand内容的

5.需要接收外部设备（external accessories)数据的（比如iPod的音响和其他各种dock）

**注：为使app能够长时间后台运行，将非上述app声明为具备上述功能的，将被拒绝上架。**




# 后台唤醒App


如何自动唤醒app去完成某项任务，且中途无需任何用户操作？

答案是significant-change location service。

开启这一服务后，当设备的位置改变并达到预设的值，则系统会自动拉起app，**即使它之前已经被系统或用户手动终止。**

整个过程没有UI交互，用户甚至完全不知道app启动过，也不会存在于最近使用过的App列表中。



启动significant-change location service：

调用CLLocationManager的startMonitoringSignificantLocationChanges方法即可启用这一服务。

stopMonitoringSignificantLocationChanges可以停止服务。



位置变更通知：

当设备位置发生变更并被检测到时，CLLLocationManager的delegate：

– locationManager:didUpdateLocations: 将被调用。

若此时app不在运行，则会被通过application:didFinishLaunchingWithOptions:拉起，并在options中包含一个Key(UIApplicationLaunchOptionsLocationKey)来标识app是被位置服务拉起的。



除了significant-change location service外， Newsstand和External Accessory类型的App也能够在满足条件时被自动唤醒。
