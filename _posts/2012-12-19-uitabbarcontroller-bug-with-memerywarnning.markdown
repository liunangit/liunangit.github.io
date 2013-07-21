---
author: admin
comments: true
date: 2012-12-19 16:43:07+00:00
layout: post
slug: uitabbarcontroller%e5%92%8c%e5%86%85%e5%ad%98%e8%ad%a6%e5%91%8a%e5%bc%95%e5%8f%91%e7%9a%84bug
title: UITabbarController和内存警告引发的bug
wordpress_id: 100
categories:
- ios
---

近期收到bug报告，app在使用时可能出现“黑屏”的情况。从截图上看，不是完全的黑屏，tabbar是显示正常的，而tabbar以上部分则消失了。看样子很像收到内存警告后，ui被移除掉了。但跟踪了代码，发现代码逻辑无问题。

在模拟器中模拟内存警告，问题始终无法重现，改用真机测试，加入模拟内存警告的代码：

    
    
    [[UIApplication sharedApplication] performSelector:@selector(_performMemoryWarning)];
    


（注意，这是一个私有api，只能用于测试，不能提交审核）



经过不懈的努力，终于发现这个bug的重现规律：

1.iOS版本为5.x（4.x和6.x均无此问题）;

2.key window的rootviewController为一个UITabbarController;

3.当前controller以present方法弹出另一个controller；

4.这时触发内存警告；

5.弹出一个UIActionSheet；

6.关闭actionsheet，并把当前present出来的dismiss掉。

此时问题必现！（只有真机才会重现）



基本可以断定这是一个iOS的bug，解决方法也简单，只需在调用showInView的时候将actionsheet加到UITabbarController的view上即可。（或者用showFromTabbar方法）



当我们认为这个bug已被解决掉的时候，又有人报告出现了这个bug！

这次的重现规律是：

1-3:同上文；

4.切到后台；

5.触发内存警告；

6.切回app，并dismiss。

此时问题必现（在模拟器上也会重现）



解决方法：在程序切回前台的时候，重设UITabbarController的selectedController即可：

    
    
    UIViewController *controller = tabbarController.selectedViewController;
    
    tabbarController.selectedViewController = nil;
    
    tabbarController.selectedViewController = controller;
    


到此黑屏的问题解决。



没过多久，又一个bug来了，在某些界面actionsheet显示不正常：

在iOS5中，弹出UIActionSheet，然后转屏，这时整个界面无法再响应任何事件！并且横屏时弹出的actionsheet也是纵向的。

看来之前改的showInView方法出问题了。原来是我们的UITabbarController不响应转屏事件，而弹出的controller又可以转屏所致。



最终解决方案：

在controller的基类中（幸亏我们的所有controller都有同一个基类），当收到内存警告时，判断当前controller是否有present出来的子controller（controller.presentedViewController)。

若有则标记该父controller，让该controller在viewDidLoad时重设selectedController。



心得：

1.有些bug真难重现（以前有个bug全组人花了2天才找到）；

2.有些bug当你认为已经解决了，它可能又从其他地方冒出来；

3.当你解决了一个bug，很可能又引发另一个bug。
