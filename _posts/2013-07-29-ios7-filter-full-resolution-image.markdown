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

<pre>
- (UIImage *)filterFullImage:(ALAssetRepresentation *)rep
{
    NSString *xmpString = rep.metadata[@"AdjustmentXMP"];
    NSData *xmpData = [xmpString dataUsingEncoding:NSUTF8StringEncoding];
    CIImage *image = [CIImage imageWithCGImage:rep.fullResolutionImage];
    
    NSError *error = nil;
    NSArray *filterArray = [CIFilter filterArrayFromSerializedXMP:xmpData
                                                 inputImageExtent:image.extent
                                                            error:&error];
    if (error) {
        NSLog(@"Error during CIFilter creation: %@", [error localizedDescription]);
        return nil;
    }
    
    CIContext *context = [CIContext contextWithOptions:nil];
    for (CIFilter *filter in filterArray) {
        [filter setValue:image forKey:kCIInputImageKey];
        image = [filter outputImage];
    }
    
    CGImageRef img = [context createCGImage:image fromRect:[image extent]];
    UIImage *resultImage = [UIImage imageWithCGImage:img scale:1.0 orientation:(UIImageOrientation)rep];
    CGImageRelease(img);
    return resultImage;
}
</pre>

是的，这个方法有一些耗时，它将原图用所选滤镜挨个处理了一遍。

另外，这个方法支持iOS6以上系统。（iOS6以前也没有系统滤镜吧）

总结：感谢万能的[Stack Overflow](http://stackoverflow.com/)。

