---
author: admin
comments: true
date: 2012-11-30 14:18:18+00:00
layout: post
slug: '%e8%87%aa%e5%ae%9a%e4%b9%89uitableviewcell%e6%a0%b7%e5%bc%8f'
title: 自定义UITableViewCell样式
wordpress_id: 90
categories:
- ios
---

虽然我觉得原生的UITableView不算丑，无奈设计同学坚持要用自己的风格，迫于压力只有对table进行自定义。


## 1.UITableViewCell的背景


对于plain形式的cell，只需要设置背景颜色和高亮颜色就行了：

    
<pre> 
    UIView *normalBgView = [[UIView alloc] initWithFrame:cellFrame];
    
    normalBgView.backgroundColor = normalColor;
    
    UIView *highlightBgView = [[UIView alloc] initWithFrame:cellFrame];
    
    highlightBgView.backgroundColor = highlightColor;
    
    cell.backgroundView = normalBgView；
    
    cell.highlightBgView = highlightBgView;
</pre> 
    


对于group形式的cell，需要判断该cell位于当前section的位置，给出对应的backgroundView和highlightBgView。

这里的位置有四种情况：位于section顶端、中间、底端、单独的section。

判断出位置后按说照plain的方法贴图就行，但诡异的事情发生了。我们的backgroundView是一个带圆角的白底图片，highlightBgView是同样大小的蓝底图片。在highlight时候蓝底图片顶端（底部)会出现一条白线，仿佛是backgroundView露在外面一样。找了半天没查到原因，最后只能在- (void)setHighlighted:(BOOL)highlighted animated:(BOOL)animated方法中，在highlight时手动更改backgroundView为蓝色图片以解决这个问题。(后来已查明问题原因是用的两张图尺寸不同。)




## 2.更改cell中textLabel的位置


直接设置cell.textLabel.frame是无效的。无奈只有写了一个类继承UITableViewCell，然后实现其layoutSubviews方法：

    
    
<pre> 
    - (void)layoutSubviews
    
    {
    
    [super layoutSubviews];
    
    self.textLabel.frame = myFrame;
    
    }
</pre> 
    




## 3.调整table中section之间的间距


想要增加间距，只需添加header或footer；想要缩小间距，同样需要header和footer。

<pre> 
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section
</pre> 

和

<pre> 
- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section
</pre> 

都返回1.0f（可调整，1.0是最小值），然后

viewForHeaderInSection和viewForFooterInSection返回一个透明的view，即可大功告成（感谢万能的stackoverflow)。

实现效果：
[![](http://liu-nan.com/wp-content/uploads/2012/11/setting-208x300.jpg)](http://liu-nan.com/wp-content/uploads/2012/11/setting.jpg)
