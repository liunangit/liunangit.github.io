---
author: liunan
comments: true
date: 2014-01-05
layout: post
title: 2013年终总结
---

2013终于尘埃落定了。这里对逝去的一年做下总结，也为新的一年定下目标。    

##成长和收获

我们的iPhone客户端日活跃用户数已经突破1000w，我个人对产品也有一些思考，有一些感触：


+    产品质量优于进度   


虽然很多产品采用“小步快跑”策略，优先保证进度抢占市场，但如果一个产品想要立志做“精品”，就必须确保高质量。质量不达标的产品坚决不发布。我们每个版本除了实现产品需求以外，还专门排了时间做“技术需求”，解决上个版本遗留的技术问题，优化和重构一些代码，这样才能保持良好的技术架构，并为产品需求服务。


+    功能贵精不贵多


一个优秀的app功能不一定要多(事实上是一定不要太多)，但每一个功能都必须围绕产品的核心目标，都要帮助用户产生价值。手机QQ被微信超越，很大一点原因就是不如微信专注，功能太多了。感觉我们的产品功能也太多了，甚至可以砍掉一半的功能后对核心体验也无影响。虽然没有统计过究竟有多少功能，但最新版本的代码量已经超过120万行，除去用到开源代码也有60余万行，由此可见一斑。
    

+    从用户角度思考，用数据说话


大部分用户其实不如我们想象的“聪明”。看过一次用户调研，很多用来方便用户使用的入口，其实用户完全无视，只会依照自己的固有思维使用产品。因此我们在决定增加功能入口前，先要看统计数据，看用户通常是怎么样使用这个功能的。好在公司非常重视统计上报，几乎每个功能都有统计，这样在做一些决策时也有数据支撑。


+    不断对技术提出更高要求


很多功能实现起来容易，但是做精做好难。比如上传下载的成功率，各个命令字的成功率，网络连通率，app系统时间，UI滑动流畅度等等，都可以量化并和竞品比较，找到我们的差距在哪，并想办法赶超。


+    对用户反馈的重视和跟进


很难想象一个1000w日活跃用户的产品，会对每一个用户的反馈进行跟进和答复。虽然痛苦，但我们的确做到了。从用户的反馈中我们的确发现了一些bug，而这些bug很多是在特殊机型或者特定网络条件下才会出现，我们自己很难测试出来。


+    曲折的商业化


移动互联网火了那么多年，却一直没有找到好的商业化方向，这是对整个行业的打击。2013年以微信为首的移动互联网产品终于开始坚定地进行商业化实践。我们的产品也加入了很多明显的“圈钱”的功能。对产品而言，这些功能明显是偏离核心目标的，可以说对用户体验有一定程度的损害；但事实就是那么残酷，再好再酷的产品要是不能盈利，终究不能走得长远。



##对公司和团队的一些看法


在目前这家公司已经待了1年半。我对公司的评价是：比上不足，比下有余；大环境不错，小环境一般。很多问题都是大公司通病，暂且不表。
老板一言堂较严重，那么多产品经理都只是所谓的“执行产品经理”，几乎所有的功能细节和设计都需要老板拍板才能做；老板加需求也很随意，完全不考虑项目排期，结果就是一加需求产品和开发就要加班。很多时候感觉自己只是个工具，对产品一点发言权都没有，只能埋头开发。


##2014年的目标

做iOS开发2年半了，很多技术或多或少都有所涉及，但是一直没有系统地总结过。2014需要花一些时间思考和整理iOS开发中常用到的技术和框架，包括但不限于:

+    绘图机制（Quartz 2D)

+    网络（CFNetwork)

+    运行时 (Runtime) 

+    并发编程（CGD/Dispatch Queues/Operation Queues/各种锁的使用)

另外这两年专注于iOS开发，技术视野太狭窄，需要学习一些其他技术扩展一下视野。
综合考虑决定选择学习ruby--实用，灵活，语法看起来不像Python一样死板，更关键的是ruby来源于Lisp，其思维方式和C系列语言有很大不同。

