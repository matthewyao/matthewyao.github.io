---
layout:		post
title: 		Fiddler抓取HTTPS流量的原理
catalog:	true
tags: 		[Window, Fiddler, Https, 前端, ]
---


## Fiddler抓取HTTPS流量的原理

> **TLS**是一种端到端的**传输层加密协议**，是HTTPS协议的一个组成部分。访问HTTPS站点时，HTTP请求、响应都通过TLS协议在浏览器和服务器之间加密传输，并且通过数字证书技术保证数据的保密性和完整性；任何“中间人”、包括代理服务器都只能转发数据，而无法窃听或者篡改数据。


要抓取HTTPS流量的明文内容，Fiddler必须解密HTTPS流量。但是，浏览器将会检查数字证书，并发现会话遭到窃听。为了骗过浏览器，Fiddler通过使用另一个数字证书重新加密HTTPS流量。Fiddler被配置为解密HTTPS流量后，会自动生成一个名为*DO_NOT_TRUST_FiddlerRoot*的CA证书，并使用该CA颁发每个域名的TLS证书。若DO_NOT_TRUST_FiddlerRoot证书被列入浏览器或其他软件的信任CA名单内，则浏览器或其他软件就会认为HTTPS会话是可信任的、而不会再弹出“证书错误”警告。

开启HTTPS流量解密功能后，Fiddler将会提示用户将DO_NOT_TRUST_FiddlerRoot证书列入IE浏览器的信任CA名单。用于调试客户端时，这已经足够了；Firefox用户也可以很方便的手动导入DO_NOT_TRUST_FiddlerRoot证书。但是，若要在服务器上抓取ASP.Net发出的HTTPS请求，这是不够的——你必须将DO_NOT_TRUST_FiddlerRoot证书导入“机器帐号”的信任CA名单。

## Fiddler抓取移动端设备HTTPS流量步骤

- 打开*tools->FiddlerOptions->HTTPS*


![image](http://oc26wuqdw.bkt.clouddn.com/1.png)

- 勾选上Capture HTTPS CONNECTs和Decrypt HTTPS traffic

![image](http://oc26wuqdw.bkt.clouddn.com/2.png)

- 然后会提示安装证书，点击yes

![image](http://oc26wuqdw.bkt.clouddn.com/3.png)

- 点击是安装证书

![image](http://oc26wuqdw.bkt.clouddn.com/4.png)

- 打开certMgr.msc可以管理  PC上的所有证书，在受信任的根证书颁发机构->证书中会有一个DO_NOT_TRUST_FiddlerRoot证书，表明证书安装成功

![image](http://oc26wuqdw.bkt.clouddn.com/5.png)

- 然后在Connections中将Fiddler的监听端口设置为8888，同时勾选上Allow remote computers to connect允许远程连接

![image](http://oc26wuqdw.bkt.clouddn.com/6.png)

- 然后在HTTPS中点击Export Root Certificate to Desktop

![image](http://oc26wuqdw.bkt.clouddn.com/7.png)

- 然后桌面上会生成一个FiddlerRoot.cer的证书

![image](http://oc26wuqdw.bkt.clouddn.com/8.png)