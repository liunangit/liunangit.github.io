---
author: admin
comments: true
date: 2012-09-11 12:54:05+00:00
layout: post
slug: '%e5%a6%82%e4%bd%95%e8%87%aa%e5%ae%9a%e4%b9%89uinavigationbar%e7%9a%84%e8%83%8c%e6%99%af%e5%9b%be%e7%89%87'
title: 如何自定义UINavigationBar的背景图片
wordpress_id: 70
categories:
- ios
---

## For iOS 5.0 or later：




### 最简单的：


直接调用setBackgroundImage:forBarMetrics:


### 另类的：



    
    
    @implementation UIView (Ext)
    
    - (UIView *)findSubview:(NSString *)name
    
    {
    
        NSString *desc = [[self class] description];
    
        if ([desc compare:name] == NSOrderedSame) {
    
            return self;
    
        } else {
    
            for (NSUInteger i = 0; i &lt; [self.subviews count]; i++) {
    
                UIView *subview = [self.subviews objectAtIndex:i];
    
                subview = [subview findSubview:name];
    
                if (subview) {
    
                    return subview;
    
                }
    
            }
    
        }
    
        return nil;
    
    }
    
    @end
    
    @implimentation UINavigationBar (Extended)
    
    - (void)setBgImage1:(UIImage *)image
    
    {
    
        UIView *v = [self findSubview:@"UINavigationBarBackground"];
    
        if (image) {
    
            CALayer * layer = [CALayer layer];
    
            layer.frame = CGRectMake(0.f, 0.f, self.frame.size.width, 50);
    
            layer.contents = (id)image.CGImage;
    
            [v.layer addSublayer:layer];
    
        }
    
    }
    
    @end
    





## For IOS 4.x




## 最常用的：




    
    
    @implementation UINavigationBar (Extended)
    
    - (void)drawrect:(CGRect)frame
    
    {
    
        //bgImage: UIImageView
    
        [bgImage drawrect:frame];
    
    }
    
    @end
    




## 最简单的：



    
    
    @implimentation UINavigationBar (Extended)
    
    - (void)setBgImage:(UIImage *)image
    
    {
    
        UIImageView *bgView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 320, 50)];
    
        bgView.image = image;
    
        [self insertSubview:background atIndex:0];
    
        [bgView release];
    
    }
    
    @end
    




注：以上两种方法不适用ios5.0

注2：在类别扩展中重写类中已有的方法并调用，5.0以前会调用到重写的方法，5.0之后不会。
