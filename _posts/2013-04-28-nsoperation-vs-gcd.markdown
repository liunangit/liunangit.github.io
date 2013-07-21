---
author: admin
comments: true
date: 2013-04-28 02:15:20+00:00
layout: post
slug: nsoperation%e4%bd%bf%e7%94%a8%e6%96%b9%e6%b3%95
title: NSOperation使用方法
wordpress_id: 137
categories:
- ios
---

NSOperation和GCD的区别，就是提供了一个简单的方法控制并发数和依赖。
在使用中，NSOperation一般有两种场景：

**1.同步执行**
这里指在工作线程中同步执行，而不需要等待网络回调。
直接使用NSOperation的子类NSInvocationOperation即可。

    
    NSOperationQueue *operationQueue = [[NSOperationQueue alloc] init];
    [operationQueue setMaxConcurrentOperationCount:4];
    
    for (int i = 0; i < 100; ++i) {
        NSInvocationOperation *operation = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(doSomeThing) object:nil];
        [operationQueue addOperation:operation];
    }


同步执行主要用于有一定并发控制，且不需要异步等待又不宜在主线程中进行的操作，比如gif编解码。

**2.异步执行**

实际使用中用到更多的就异步NSOperation，典型的例子就是网络请求的并发控制。

    
    NSOperationQueue*operationQueue = [[NSOperationQueue alloc] init]; 
    [operationQueue setMaxConcurrentOperationCount:4]; //4个并发请求
    
    for (int i = 0; i < 100; ++i) {
        MyTask *operation = [[NSInvocationOperation alloc] init];
        [operationQueue addOperation:operation];
    }


实现时需要实现一个NSOperation的子类，并实现start方法:

    
    - (void)start
    {
        [self startRequest];
    }


这里startRequest发送一个网络请求，同时监听网络回包。

收到回包之后，我们会发现operationQueue没有往下走，operationQueue不知道这个task已经完成了。

原因是没有实现isFinished方法。task开始运行时isFinish返回NO，任务结束后应当返回YES。

系统何时调用该方法呢？答案是产生一个KVO通知，告诉系统isFinished状态改变了。

    
    - (void)didRecevieResponse
    {
        [self willChangeValueForKey:@"isFinished"];
        getResponse = YES;
        [self didChangeValueForKey:@"isFinished"];
    }
    
    - (BOOL)isFinished
    {
        return getResponse;
    }


另外，isExecuting、isConcurrent等方法也应该被实现。
