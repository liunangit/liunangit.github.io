---
author: admin
comments: false
date: 2012-11-06 23:02:50+00:00
layout: post
slug: ios6%e6%96%b0%e7%89%b9%e6%80%a7%e4%b9%8buicollectionview
title: iOS6新特性之UICollectionView
wordpress_id: 80
categories:
- ios
---

## **什么是UICollectionView？**


UICollectionView是iOS6提供的一个新类，用来显示用户的序列化数据（ordered data），也就是Pinterest的“瀑布流”，特别适合用来展示照片列表或杂志封面列表。


## **如何使用UICollectionView?**


UICollectionView和UITableView非常相似:

1. UITableView有对应的UITableViewController、UITableViewCell、UITableViewDelegate、UITableViewDataSource，

UICollectionView有对应的UICollectionViewController、UICollectionViewCell、UICollectionViewDelegate、UICollectionViewDataSource；

2. UITableView和UICollectionView都是通过其delegate和datasource的一系列回调来获得其cell的外观和数据；

3 UITableView和UICollectionView都有cell复用机制；

4. UITableView和UICollectionView都有设置header和footer的方法。

同时两者又有不同之处：

1. UITableView的每行只能显示一个cell，而UICollectionView可以显示多个；

2. UICollection有专门控制布局管理的类（UICollectionViewLayout），而UITableView没有。

假如你使用过UITableView，那么用UICollectionView做一个照片列表的展示页将非常简单：

1. 初始化一个layout，这里使用系统提供的UICollectionViewFlowLayout:

<pre>
UICollectionViewFlowLayout *flowLayout =[[UICollectionViewFlowLayout alloc] init];

flowLayout.itemSize = CGSizeMake(100, 80); //设置每个cell的size
</pre>

2. 使用这个layout实例化一个UICollectionView对象：

<pre>
NormalLayoutViewController *collectionController = [[NormalLayoutViewController alloc] initWithCollectionViewLayout:flowLayout];


[self.navigationController pushViewController:collectionController animated:YES];
</pre>

在这里，NormalLayoutViewController为UICollectionView的一个子类。

3. 在NormalLayoutViewController中实现相关的delegate：

返回item数量:

    
<pre>
    - (NSInteger)collectionView:(UICollectionView *)view numberOfItemsInSection:(NSInteger)section
</pre>


返回每个cell：

    
<pre>
    - (UICollectionViewCell *)collectionView:(UICollectionView *)cv cellForItemAtIndexPath:(NSIndexPath *)indexPath
</pre>


这样一个相片列表的雏形就出来了。

当然collection view中还有很多属性用来控制ui的显示方式，可以在官方文档中查询到。



**什么是UICollectionViewLayout？**

假如没有UICollectionViewLayout，UICollectionView只能算UITableView的增强版罢了。



UICollectionViewLayout是一个抽象类，在使用UICollectionView时必须创建一个UICollectionViewLayout的子类，定义cell的位置等布局信息。当然我们并不是一定要自己定义这样一个子类，因为SDK中提供了一个现成的子类--UICollectionViewFlowLayout,很多情况下我们可以直接使用或者继承UICollectionViewFlowLayout。



有三种类型的元素需要layout：

cell：collection view中的主要元素，对应UITableView中的一个cell；

supplementary view：可选的（optional）view，在界面上展示，但不可以被用户选择的元素，可用来实现header和footer；

decoration view：另一种supplementary view，装饰整个collection view，一般用于背景。



每个layout都应当实现以下方法：

<pre>    
    - (CGSize)collectionViewContentSize
</pre>    


返回一个collection view item的size



    
<pre>    
    - (NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
</pre>    


为所有item返回一个layout attributes数组，数组中元素的类型为UICollectionViewLayoutAttributes。UICollectionViewLayoutAttributes记录了一个layout的位置、大小、透明度等信息。

注意，每一个可见的item都需要对应的layout attribute，包括cell、supplementary view和decoration view



    
<pre>    
    - (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath （可选）
</pre>    


为一个特定的item返回layout attribute。注意，返回的attribute仅使用cell，不包括supplementary view和decoration view。



    
<pre>    
    - (UICollectionViewLayoutAttributes *)layoutAttributesForSupplementaryViewOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath（可选）
</pre>    


为一个特定的supplementary返回attribute（只在使用了supplementary view时需要）。其中，kind是一个用来标识supplementary view种类的字符串。



    
<pre>    
    - (UICollectionViewLayoutAttributes *)layoutAttributesForDecorationViewOfKind:(NSString *)kind atIndexPath: (NSIndexPath *)indexPath（可选）
</pre>    


为一个特定的decoration返回attribute（只在使用了supplementary view时需要）。



    
<pre>    
    - (BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds（可选）
</pre>    


当bounds改变时，layout是否需要刷新。默认返回NO。



此外，当collection view中的数据发生变化（例如删除或添加了item），以下方法可能会被调用：

    
<pre>    
    initialLayoutAttributesForAppearingItemAtIndexPath:
    
    initialLayoutAttributesForAppearingSupplementaryElementOfKind:atIndexPath:
    
    initialLayoutAttributesForAppearingDecorationElementOfKind:atIndexPath:
    
    finalLayoutAttributesForDisappearingItemAtIndexPath:
    
    finalLayoutAttributesForDisappearingSupplementaryElementOfKind:atIndexPath:
    
    finalLayoutAttributesForDisappearingDecorationElementOfKind:atIndexPath:
</pre>    


与前面介绍的方法相似，这些方法分别对应不同类型的元素（cell、supplementary和decoration）， 通常和item出现/消失时的动画效果联合使用。



附件中的例子展示了UICollectionView的使用：

![图1](/images/collectionview/collectionview_1.jpg)

图1


图1所示的collection view中使用了系统提供的UICollectionViewFlowLayout，可用很简短的代码实现照片列表效果。





![图2](/images/collectionview/collectionview_2.jpg)
图2


图2所示的例子实现了单行的collection view，其layout继承自UICollectionViewFlowLayout，将scrollDirction设置为水平方向，并重写了layoutAttributesForElementsInRect方法。



![collectionview3](/images/collectionview/collectionview_3.jpg)

图3


图3所示例子实现了一个环形的collection view，重写了layoutAttributesForItemAtIndexPath:、layoutAttributesForElementsInRect:、initialLayoutAttributesForInsertedItemAtIndexPath:、finalLayoutAttributesForDeletedItemAtIndexPath：等几个方法用于布局信息和cell的出现/消失动画。

观察这些方法和代理的返回值不难发现，他们要不就返回一个UICollectionViewLayoutAttributes对象，要不就返回一个UICollectionViewLayoutAttributes对象的数组。事实上，UICollectionView长什么样，正是由UICollectionViewLayoutAttributes决定的。

**什么是UICollectionViewLayoutAttributes？**

顾名思义，它提供了collectionview布局所需的各种属性，其中包括：



	
  * 每个元素的尺寸和位置（size、center）

	
  * 透明度（opacity）

	
  * 变化矩阵（transform）

	
  * 层次关系（zIndex）


**什么是UICollectionViewFlowLayout？**

iOS6中系统提供的layout。从名字中可以看出它是一种线性布局。事实上，它能够满足我们的绝大部分需要。使用UICollectionViewFlowLayout，我们可以方便地设置：



	
  * 每个item的大小

	
  * 行间距

	
  * item之间的间距

	
  * 滚动方向

	
  * header和footer的大小

	
  * 每个section距离四周的距离（Insets)


本文提供的demo全部可用UICollectionViewFlowLayout实现。

** iOS7中UICollectionView的新特性**

iOS7新增了一个Layout：UICollectionViewTransitionLayout，用来进行两个collectionview之间的平滑切换。和UIViewControllerInteractiveTransitioning联合使用，能够实现很棒的交互体验。

**总结**

目前市场上已经有很多应用实现了collection view的效果，并出现了相关开源库，但无疑官方提供的api更加高效易用，尤其是其layout机制非常灵活，可以用来实现很多很炫的效果。

然而考虑到我们的app不太可能只支持iOS6，因此在一段时间内还得使用自己的collection view。

下载文中的demo请猛击[这里](https://github.com/liunangit/CollectionViewDemo)。



参考资料：

1.Collection View Programming Guide for iOS: [https://developer.apple.com/library/ios/#documentation/WindowsViews/Conceptual/CollectionViewPGforIOS/Introduction/Introduction.html](https://developer.apple.com/library/ios/#documentation/WindowsViews/Conceptual/CollectionViewPGforIOS/Introduction/Introduction.html)

2.UICollectionView Class Reference: [https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UICollectionView_class/Reference/Reference.html](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UICollectionView_class/Reference/Reference.html)

3.UICollectionViewLayout Class Reference: [https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UICollectionViewLayout_class/Reference/Reference.html](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UICollectionViewLayout_class/Reference/Reference.html)
