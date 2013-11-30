---
layout:     post
title:      通过ALAsset获取编辑过的图片 
category:   ios
---

一般情况下，通过一个ALAsset对象获取图片的方法是：

<pre>
CGImageRef imageRef = defaultRepresentation.fullResolutionImage;
</pre>

如果一张图片被编辑过（iOS 7的系统自带滤镜，说的就是你！)，那么通过上述方法只能获取到原图。通过这个方法可以判断图片是否编辑过：

<pre>
- (BOOL)hasBeenEdited:(ALAsset *)asset
{
    return [asset.defaultRepresentation.metadata objectForKey:@"AdjustmentXMP"];
}

</pre>

如果编辑过，这样便可以获取到编辑后的图片：

<pre>
GImageRef imageRef = defaultRepresentation.fullScreenImage;
</pre>

但是，这样获取到的图片分辨率有所损失，适配了屏幕尺寸。

下面这段代码能够拿到原始尺寸且编辑过的图片：

![code](/images/filter-full-resolution/filter-full-resolution.jpg)

注意：这个方法可能会非常耗时。在iPhone4上曾遇到过某些照片需要处理数分钟之久！因此请酌情处理一些屌丝机型！

另外，这个方法支持iOS6以上系统。（iOS6以前也没有系统滤镜吧）

总结：感谢万能的Stack Overflow。
