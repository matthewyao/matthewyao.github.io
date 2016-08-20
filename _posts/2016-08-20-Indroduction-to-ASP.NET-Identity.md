---
layout:     post
title:      Indroduction to ASP.NET Identity
subtitle:   ASP.NET Identity was designed to solve site membership requirements.
date:       2016-08-20 16:00:00
catalog:    true
tags:       [后端, .NET, Identity, C#, ]
---

## Indroduction to ASP.NET Identity

#### Why Identity?

> [ASP.NET Identity](http://www.asp.net/identity) was designed to solve site membership requirements.


 
### Advantageof ASP.NET Identity
![advantage](http://oc26wuqdw.bkt.clouddn.com/blog/identityadvantage.jpg)
微软在*.NET Framework 4.5* 中推出了**ASP.NET Identity**，它为ASP.NET 应用程序提供了一系列的API用来管理和维护用户，有以下优点：
易于集成：ASP.NET Identity 可以用在所有的 ASP.NET 框架上，例如 **ASP.NET MVC, Web Forms，Web Pages，ASP.NET Web API**

**1. 持久化**
默认情况下，ASP.NET Identity将用户所有的数据存储在数据库中。ASP.NET Identity 使用 Entity Framework 实现其所有的检索和持久化机制。
通过Code First,你可以对数据库架构的完全控制，一些常见的任务例如改变表名称、改变主键数据类型等都可以很轻易地完成。

**2. 基于声明的**
ASP.NET Identity 支持基于声明的身份验证，它使用一组"声明"来表示用户的身份标识。相对于"角色"，"声明"能使开发人员能够更好地描述用户的身份标识。"角色"本质上只是一个布尔类型（即"属于"或"不属于"特定角色），而一个"声明"可以包含更多关于用户标识和成员资格的信息。
 
### ASP.NETIdentity主要组成部分

 ![mainpart](http://oc26wuqdw.bkt.clouddn.com/blog/identitymainpart.jpg)
 
`ApplicationUser`和`ApplicationDbContext`分别继承自己`Microsoft.AspNet.Identity.EntityFramework`的`IdentityUser`和`IdentityDbContext`。但与用户相关的操作实际上是通过`Microsoft.AspNet.Identity.Core`的 `UserManager`类来完成的，而`UserManager`的所有操作最终是由`UserStore`实现。

 ![usermanager](http://oc26wuqdw.bkt.clouddn.com/blog/identityusermanager.jpg)
 
### 传统ASP.NET身份验证方式
安全问题一直是ASP.NET的关注点。其中，Windows验证和表单验证（Forms Authentication）就是ASP.NET两种主要的安全机制。
 
**1. Windows验证**
一般用于局域网应用。使用Windows验证时，用户的Windows安全令牌在用户访问整个网站期间使用HTTP请求，进行消息发送。应用程序会使用这个令牌在本地（或者域）里验证用户账号的有效性，也会评估用户所在角色所具备的权限。当用户验证失败或者未授权时，浏览器就会定向到特定的页面让用户输入自己的安全凭证（用户名和密码）。
 
**2. Forms验证**
Windows验证的局限性非常明显，一旦用户有超出本地域控制器范围的外网用户访问网站，就会出现问题。ASP.NET表单验证（Forms Authentication）很好的弥补了这一缺陷。使用表单验证，ASP.NET需要验证加密的HTTP cookie或者查询字符串来识别用户的所有请求。cookie与ASP.NET会话机制（session）的关系密切，在会话超时或者用户关闭浏览器之后，会话和cookie就会失效，用户需要重新登录网站建立新的会话。
 
 
### ASP.NETIdentity验证的原理
讲到Identity的验证方式就绕不过Claims的认证方式，Claims认证最大的好处就是简单的隔离了验证(**Authentication**)和授权(**Authorization**)两个部分，但这也是它最大的优势。

![authentication](http://oc26wuqdw.bkt.clouddn.com/blog/identityauthentication.jpg)
![code1](http://oc26wuqdw.bkt.clouddn.com/blog/identityauthentication_validate.jpg)

.NET下Claims-based认证的主要由`ClaimsIdentity`以及`ClaimsPrincipal`这两个类组成，`ClaimsIdentity`可以理解为携带用户信息的证书，而`ClaimsPrincipal`可以理解为应用颁发的令牌，当然，认证机构可以是应用本身，也可以是实现**Owin(OpenWeb Interface for .NET)**方式的第三方认证。

#### Demo
![code1](http://oc26wuqdw.bkt.clouddn.com/blog/identitycode1.jpg)
![code2](http://oc26wuqdw.bkt.clouddn.com/blog/identitycode2.jpg)



