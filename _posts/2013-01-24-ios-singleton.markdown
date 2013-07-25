---
author: admin
comments: true
date: 2013-01-24 09:55:56+00:00
layout: post
slug: ios%e4%b8%ad%e7%9a%84singleton
title: iOS中的Singleton
wordpress_id: 129
categories:
- ios
---

Singleton(单件)在iOS开发中被广泛使用。我们常用的NSNotificationCenter、NSUserDefaults、NSFileManager等类都是使用单件模式。我们自己写代码也经常用到单件。

**最简单的Singleton:**

	static Singleton *s_single;
    
    + (id)sharedSingle
    
    {
    
    if (!s_single) {
    
    s_single = [[self alloc] init];
    
    }
    
    return s_single;
    
    }

很明显，上面这个方法没有考虑多线程。
**多线程版的：**

    
    + (id)sharedSingle
    
    {
    
    @synchronized(self) {
    
    if (!s_single) {
    
    s_single = [[self alloc] init];
    
    }
    
    return s_single;
    
    }
    
    }


作为一个轻度强迫症的注重效率的程序猿，无法忍受每次都加锁。
**加锁改进版：**

    
    + (id)sharedSingle
    
    {
    
    if (!s_single) {
    
    @synchronized(self){
    
    if (!s_single) {
    
    s_single = [[self alloc] init];
    
    }
    
    }
    
    }
    
    return s_single;
    
    }


只有第一次需要加锁，看起来很好。但有人说这个不是线程安全的！读s_single的操作不是原子的！虽然在我自己的测试代码中没有发现线程问题，但作为一个追求完美的程序猿，继续改进：

**接近完美版**
    
    + (id)sharedSingle
    
    {
    
    static  dispatch_once_t pred;
    
    dispatch_once(&pred, ^{
    
    s_single = [[self alloc] init];
    
    });
    
    return s_single;
    
    }


到此应该基本OK了

最后，我们重写alloc方法，禁止外部alloc一个Singleton:

    
    + (id)alloc
    
    {
    
    NSLog(@"alloc a Singleton are not allowed!");
    
    return nil;
    
    }
    
    + (id)privateAlloc
    
    {
    
    return [super alloc];
    
    }


最后的最后，[这里](http://tech.puredanger.com/2007/07/03/pattern-hate-singleton/)有一个讨厌单件模式，但又不得不用的程序猿。

看别人纠结真是一件欢乐的事情。
