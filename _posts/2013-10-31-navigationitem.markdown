---
author: admin
comments: true
date: 2013-10-31
layout: post
title: rightBarButtonItems显示不全的问题
---

今天接到一个需求，要在导航栏右边加两个按钮，就像这样：

![navigationitems1](/images/navigationitems/navigationitems1.jpeg)

一看文档，很简单，设置navigationItem的rightBarButtonItems属性即可：

<pre>
@property(nonatomic,copy) NSArray *rightBarButtonItems NS_AVAILABLE_IOS(5_0);
</pre>

搞定收工！

第二天测试同学找上门了，说是某些时候只能显示出一个item：

![navigationitems2](/images/navigationitems/navigationitems2.jpeg)

这个问题在iOS7上不出现，5和6上都有，在title比较长的时候出现。
检查后发现，我们的navigationItem.titleView是一个自定义的UILabel，当这个label的长度大于一定值时就导致rightBarButtonItems显示不全；用系统默认的title就没有问题，在iOS7上也没问题。这是iOS的bug吗？

解决方案：手动减小titleView的长度，使之不超过某个指定值:

<pre>
- (void)setTitle:(NSString *)title
{
    [super setTitle:title];
    [self sizeTitleViewToFit];
}

- (void)sizeTitleViewToFit
{
    if (titleView.frame.size.width > MAX_TITLEVIEW_LENGTH) {
        CGRect frame = titleView.frame;
        frame.size.width = MAX_TITLEVIEW_LENGTH;
        titleView.frame = frame;
    }
}
</pre>
