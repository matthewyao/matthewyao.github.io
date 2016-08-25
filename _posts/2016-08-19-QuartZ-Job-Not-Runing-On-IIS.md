---
layout:     post
title:      Quartz Job在IIS下未自动运行
catalog:    true
tags:       [.NET, C#, QuartZ, 后端, IIS, ]
description: Quartz Job在IIS下未自动运行的解决方案
---


## Quartz Job在IIS下未自动运行

>IIS 为优化服务器性能，会自动对它认为休眠的应用程序进行资源回收，资源回收将会导致网站应用程序关闭。

网络上普遍有两种解决方案：1.优化IIS服务器配置 2.设置程序休眠重启

#### 优化IIS服务器配置

第一种方式优化IIS配置，参考自stackoverflow

>One way to conserve system resources is to configure **idle time-out** settings for the worker processes in an application pool. When these settings are configured,a worker process will shut down after a specified period of inactivity. The default value for idle time-out is **20 minutes**.If you have a just a few sites on your server and you want them to always load fast then **set this to zero**.  ------*stackoverflow*

1. 从网上查资料发现IIS服务器为了优化性能默认配置休眠时间为20分钟，所以手动设置闲置超时为0，固定回收时间间隔为0，让IIS不主动回收闲置进程
2. 设置每天固定在凌晨2:00（错开访问高峰）回收进程，且在虚拟内存和专用内存占用超过1G（服务器16G内存）时回收进程，防止内存溢出。设置1G是基于目前系统工作线程内存占用普遍不高，便于内存溢出时及时回收进程

在这样设置后，发现任务依然没有执行，且程序有许多异常的报错信息，查了许多资料依然无法解决

#### 设置程序休眠重启

第二种方式是尝试在IIS关闭进程时捕捉关闭事件并访问应用使应用重启，虽然看起来没有第一种方式好，但却解决了实际问题。由于IIS在关闭Web应用后会触发`Application_End`事件，所以我们可以在`Application_End`事件中增加访问应用的Http request使应用重启

<pre class="prettyprint">
protected void Application_End(object sender, EventArgs e)
{
    log.Info("Server was shuting down......");
    try
    {
        HttpWebRequest req = (HttpWebRequest)WebRequest.Create("http://localhost/");
        HttpWebResponse rsp = (HttpWebResponse)req.GetResponse();
        string desc = rsp.StatusDescription;
        log.Info("Server is restarting,description：" + desc);
    }
    catch (Exception e)
    {
        log.Info("Server restart error：" + e.Message);
    }
}
</pre>

问题虽然得到解决，但并不漂亮，后续继续研究IIS的配置，使用第一种方式解决问题。

#### 关于内存溢出的说明

ASP.NET的GC机制存在下面两个问题
- GC并不是能释放所有的资源。它不能自动释放***非托管资源***
- GC并不是实时性的，这将会造成系统性能上的瓶颈和不确定性
由于GC的不实时，所以有了IDisposable接口，IDisposable接口定义了Dispose方法，这个方法用来供程序员显式调用以释放非托管资源。使用using语句可以简化资源管理。

***非托管资源:***　

- ApplicationContext
- Brush
- Component
- ComponentDesigner
- Container
- Context
- Cursor
- FileStream
- Font
- Icon
- Image
- Matrix
- Object
- OdbcDataReader
- OleDBDataReader
- Pen
- Regex
- Socket
- StreamWriter
- Timer
- Tooltip
- 文件句柄
- GDI资源
- 数据库连接