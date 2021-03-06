---
layout: post
title: 什么是控制反转
# subtitle: 关于Spring的知识点
cover-img: /assets/img/spring/bg.webp
thumbnail-img: /assets/img/spring/spring-logo.svg
tags: [整理总结,spring,ioc]
---

# 什么是控制反转（IOC）？
首先，```控制反转```这个概念因为难以理解，Martin Fowler最后提出了用```依赖注入（DI）```代替它。依赖注入确实更容易理解整套实现逻辑，但是我们扔要从控制反转的角度去理解一下。  

“控制”指的是什么？如何反转？  
```控制```指的是对实例的创建；```反转```指的是从实例的创建转交给第三方负责  

下面结合类图理解一下：     

当我们只是使用面向接口而没有使用Spring框架的时候，因为业务类承担了创建实体类的职责，所以业务类是依赖是实体类的
![](/assets/img/spring/IOC-1.PNG)

当我们使用了Spring框架的时候，实体类的创建职责其实是交给了框架，所以业务类也就不再直接依赖于实例类
![](/assets/img/spring/IOC-2.PNG)
