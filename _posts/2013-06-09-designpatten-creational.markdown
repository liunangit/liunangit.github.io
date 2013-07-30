---
author: admin
comments: true
date: 2013-06-09 15:46:23+00:00
layout: post
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f%e4%b9%8b%e5%88%9b%e5%bb%ba%e5%9e%8b%e6%a8%a1%e5%bc%8f'
title: 设计模式之创建型模式
wordpress_id: 168
categories:
- 设计模式
---

创建型的5种模式：

**工厂方法（factory method）**

**抽象工厂（abstract factory）**

**生成器（builder）**

**原型（prototype）**

**单件（singleton）**


其中，工厂方法和抽象工厂比较相似。
工厂方法的特点：一个工厂对应多种product（product具有相同的接口）
抽象工厂的特点：多个工厂对应多种product（工厂具有相同接口，product也有相同接口）
可见，工厂方法是抽象工厂的一种特例。







工厂方法：




![factorymethod](/images/designpatten_creational/factorymethod.png)

抽象工厂：




![abstractfactory](/images/designpatten_creational/abstractfactory.png)






生成器和原型非常相似：
使用者通过统一的接口生成多种product(product具有相同接口）
不同之处在于：
1.builder中使用者和生产者具有相同的生命周期，而prototype中两者的生命周期不同；
2.builder的重点在于具体product的创建过程，而prototype强调使用者对product的掌控。









生成器：




![builder](/images/designpatten_creational/builder.png)







原型：




![prototype](/images/designpatten_creational/prototype.png)









如果将工厂方法和抽象工厂看做一类，builder和prototype看做一类，那么这两类模式也非常相似：都是创建各类具有统一接口的产品。
不同之处在于：前者重点是生产product，后者重点是使用product。







在实践中，完全遵循这些模式的情况比较少，毕竟模式间的界限也比较模糊。




结合实际情况灵活应用是关键。



