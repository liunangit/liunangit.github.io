---
author: admin
comments: true
date: 2012-07-04 11:33:50+00:00
layout: post
slug: '%e5%a6%82%e4%bd%95%e6%9f%a5%e7%9c%8b%e5%bd%93%e5%89%8d%e6%98%af%e4%bd%95%e7%a7%8dios%e8%ae%be%e5%a4%87'
title: 如何查看当前是何种IOS设备
wordpress_id: 50
categories:
- ios
---

使用uname函数可以获得当前设备的名称、系统版本等信息：

    
    
    + (NSString *)deviceName
    {
         struct utsname uDevice;
        uname (&uDevice);
        NSString * platform = [NSString stringWithCString:uDevice.machine encoding:NSUTF8StringEncoding];
        if (platform) {
            if ([platform isEqualToString:@"iPhone1,1"]) {
               return [NSString stringWithFormat:@"iPhone1G GSM"];
            }
            if ([platform isEqualToString:@"iPhone1,2"]) {
                return [NSString stringWithFormat:@"iPhone3G GSM"];
            }
            if ([platform isEqualToString:@"iPhone2,1"]) {
                return [NSString stringWithFormat:@"iPhone3GS GSM"];
            }
            if ([platform isEqualToString:@"iPhone3,1"]) {
                return [NSString stringWithFormat:@"iPhone4G GSM"];
            }
            if ([platform isEqualToString:@"iPhone3,3"])  {
                return [NSString stringWithFormat:@"iPhone4G CDMA"];
            }
            if ([platform isEqualToString:@"iPhone4,1"]) {
                return [NSString stringWithFormat:@"iPhone4GS"];
            }
            if ([platform isEqualToString:@"iPhone5,1"])  {
                return [NSString stringWithFormat:@"iPhone5"];
            }
            if ([platform isEqualToString:@"iPod1,1"]) {
                return [NSString stringWithFormat:@"iPod_touch_1G"];
            }
            if ([platform isEqualToString:@"iPod2,1"]) {
                return [NSString stringWithFormat:@"iPod_touch_2G"];
            }
            if ([platform isEqualToString:@"iPod3,1"]) {
                return [NSString stringWithFormat:@"iPod_touch_3G"];
            }
            if ([platform isEqualToString:@"iPod4,1"]) {
                return [NSString stringWithFormat:@"iPod_touch_4G"];
            }
            if ([platform isEqualToString:@"iPad1,1"]) {
                return [NSString stringWithFormat:@"iPad WiFi"];
            }
            if ([platform isEqualToString:@"iPad2,1"]) {
                return [NSString stringWithFormat:@"iPad2 WiFi"];
            }
            if ([platform isEqualToString:@"iPad2,2"]) {
                return [NSString stringWithFormat:@"iPad2 GSM"];
            }
            if ([platform isEqualToString:@"iPad2,3"]) {
                return [NSString stringWithFormat:@"iPad2 CDMA"];
            }
            if ([platform isEqualToString:@"iPad2,4"]) {
                return [NSString stringWithFormat:@"iPad2 CDMAS"];
            }
            if ([platform isEqualToString:@"iPad3,1"]) {
                return [NSString stringWithFormat:@"iPad3 WiFi"];
            }
            if ([platform isEqualToString:@"iPad3,2"]) {
                return [NSString stringWithFormat:@"iPad3 GSM"];
            }
            if ([platform isEqualToString:@"iPad3,3"]) {
                return [NSString stringWithFormat:@"iPad3 CDMA"];
            }
            if ([platform isEqualToString:@"i386"] || [platform isEqualToString:@"x86_64"]) {
            return [NSString stringWithFormat:@"simulator"];
            }
        }
        return [NSString stringWithFormat:@"unknow"];
    }
    


更多详情，请咨询man 3 uname
