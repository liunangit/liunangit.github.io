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

