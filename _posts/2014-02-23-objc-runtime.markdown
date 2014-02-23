---
author: liunan
comments: true
date: 2014-02-23
layout: post
title: Objective-C runtime初探
---

#### Objective-c对象模型

**1.NSObject**

平时我们用到的类几乎都是继承自NSObject，那么NSObject里到底有些什么呢？

![NSObject](/images/objc-runtime/code1.jpg)

NSObject结构中唯一的成员变量就是isa指针。再来看看Class结构中有些什么？

![Class](/images/objc-runtime/code2.jpg)

![objc_class](/images/objc-runtime/code3.jpg)

从这个结构中可以看出，objc2以前，objc_class中有方法列表、协议列表、类名等成员变量，那么objc2中这些属性放到哪里去了呢？我们看看NSObject的alloc方法：
     
![alloc](/images/objc-runtime/code4.jpg)

该方法一直调用到

![_objc_rootAlloc](/images/objc-runtime/code5.jpg)

这里发现一个宏定义

<pre>
#define newcls(cls) ((class_t *)cls)
</pre>

![class_t](/images/objc-runtime/code6.jpg)


原来objc2把Class内的成员隐藏起来了，需要时通过newcls将Class强制转换为class_t使用。并且每一个类还记录了自己的子类和兄弟类，为objc的动态特性提供支持。

**2.isa指针**

NSObject、Class、id等重要结构中，第一个成员都是isa指针，可见isa在runtime中扮演着比较重要的角色。那么isa具体指向什么呢？这里不得不先说一下原类（meta class）的概念。

在objc中，类也是一个对象，那它也必须是另一个类的实列，这个类就是元类(metaclass)。元类保存了一个类的方法列表，因此我们可以给一个类发送消息（静态方法）。元类何时被创建呢？答案是在objc_allocateClassPair方法中。这个方法创建了一对class，但是只返回了一个class，另外那个就是元类了。
让我们回答刚才的问题，isa指向什么？

+ 一个对象的isa指向它所属的类；

+ 一个类的isa指向它的元类；

+ 元类的isa指向根元类（root class）

+ 根元类的isa指向它自身

下面这张图描述了isa的指向（以及类的继承关系)，该图片来自[这里](http://www.sealiesoftware.com/blog/class%20diagram.pdf)。

![isa](/images/objc-runtime/isa.jpg)


#### 消息 
在Objective-C中，所有的方法调用（[receiver message]）都会转换为objc_msgSend()实现。objc_msgSend是汇编实现的，具体干了什么？[这里](http://www.friday.com/bbum/2009/12/18/objc_msgsend-part-1-the-road-map/)有文章对该方法进行了分析并得出以下结论：

![objc_msgSend](/images/objc-runtime/msg_send.jpg)

另外[这里](http://www.mulle-kybernetik.com/artikel/Optimization/opti-9.html)有人给出了一个C实现的版本，看起来顺眼多了：

![objc_msgSend_c](/images/objc-runtime/msg_send_c.jpg)


#### runtime的应用

**1.method swizzling**

method swizzling用来改变某个已存在的方法的实现。
如果我们想要将一个类的某个方法替换成自己的实现，可以在该类的category中实现同名方法将原来的方法覆盖掉；但是想要在我们实现的方法中调用原方法，就需要用到一些runtime api了。

method swizzling本质上是通过系统方法method_exchangeImplementations交换两个方法的实现(IMP)达到目的的。关于method swizzling更多讨论请看[这里](http://blog.csdn.net/yiyaaixuexi/article/details/9374411)和[这里](http://esoftmobile.com/2014/02/19/method-swizzling/)

**2.关联对象（Associated Objects）**

关联对象的最大用处是为已存在的类添加属性。

我们可以通过objc_setAssociatedObject/objc_getAssociatedObject动态关联某个值，从而实现属性的扩展：

![associated_demo](/images/objc-runtime/code7.jpg)

关于Associated Objects的更多讨论请看[这里](http://esoftmobile.com/2014/02/18/associated-objects/)。

