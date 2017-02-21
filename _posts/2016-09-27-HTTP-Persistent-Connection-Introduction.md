---
layout: post
title: HTTP 持久连接简介
description: "HTTP 持久连接简介"
category: Web
avatarimg:
tags: [Website, HTTP, KeepAlive]
duoshuo: true
---

HTTP 持久连接简介

# 引言

最近被问到 HTTP 持久连接的优点和缺点，之前没系统总结过，这次重新学习简单总结下。

# HTTP 持久连接简介
<pre>
Web 客户端经常会打开到同一个站点的连接。
比如，一个 Web 页面上的大部分内嵌图片通常都来自同一个 Web 站点，
而且相当一部分指向其他对象的超链通常都指向同一个站点。
因此，初始化了对某服务器 HTTP 请求的应用程序很可能会在不久的将来对那台服务器发起更多的请求（比如，获取在线图片）。
这种性质被称为站点局部性（site locality）。

因此，HTTP/1.1（以及 HTTP/1.0 的各种增强版本） 允许 HTTP 设备在事务处理结束之后将 TCP 连接保持在打开状态，
以便为未来的 HTTP 请求重用现存的连接。
在事务处理结束之后仍然保持在打开状态的 TCP 连接被称为持久连接。
非持久连接会在每个事务结束之后关闭。
持久连接会在不同事务之间保持打开状态，直到客户端或服务器决定将其关闭为止。

</pre>


# HTTP 持久连接优点

重用已对目标服务器打开的空闲持久连接，就可以避开缓慢的 TCP 连接建立阶段。
而且，已经打开的 TCP 连接还可以避免慢启动的拥塞适应阶段，以便更快速地进行数据的传输。  

持久连接降低了时延和连接建立的开销，将连接保持在已调谐状态，而且减少了打开连接的潜在数量。

# HTTP 持久连接缺点

但是，管理持久连接时要特别小心，不然就会累积出大量的空闲 TCP 连接，耗费本地以及远程客户端和服务器上的资源。


# Ref
《HTTP 权威指南》第 4 章 连接管理 4.5 持久连接  
[KeepAlive，你优化了吗](http://51write.github.io/2014/04/09/keepalive/)  