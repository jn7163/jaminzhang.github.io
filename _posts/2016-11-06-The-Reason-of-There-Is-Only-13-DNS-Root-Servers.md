---
layout: post
title: 只有 13 台 DNS 根域名服务器原因
description: "只有 13 台 DNS 根域名服务器原因"
category: DNS
avatarimg:
tags: [DNS, UDP, TCP, Anycast]
duoshuo: true
---

# 引言

我们应该经常听到就 DNS 根域名服务有 13 台，那么是为什么呢？
今天我们来深入了解下。

# 根域名服务器简介

<pre>

根域名服务器（英语：root name server）是互联网域名解析系统（DNS）中最高级别的域名服务器，
负责返回顶级域名的权威域名服务器的地址。
截至 2014 年 10 月，全球有 504 台根服务器，被编号为 A 到 M 共 13 个标号。

大部分借由任播（Anycast）技术，编号相同的根服务器使用同一个 IP，
504 台根服务器总共只使用 13 个 IP，因此可以抵抗针对其所进行的分布式拒绝服务攻击（DDoS）。

中国大陆在北京有三台编号为 L 的镜像，编号为 F、I、J 的镜像各一台，共6台；
香港有编号为 D、J 的镜像各 2 台，编号为A、F、I、L的镜像各一台，共8台；
台湾则有编号为 F、I、J 各一台，共 3 台。

全球 13 组根域名服务器以英文字母 A 到 M 依序命名，
网域名称格式为“字母.root-servers.org”。其中有 11 个是以任播技术在全球多个地点设立镜像站。

所有根域名服务器都是以同一份根域文件返回顶级域名权威服务器（包括通用顶级域和国家顶级域），
文件只有 550KB 大小。
截至 2004 年 12 月 12 日，一共记录了 258 个顶级域和 773 个不同的顶级域权威服务器。
对于没被收录的顶级域，通过根域名服务器是没法查出相应的权威服务器。

</pre>

从上面的说明可以看出，DNS 根域名服务器并不真的只有 13 台，而是 13 个 IP，对应了 A-M 13 个编号，  
借由任播（Anycast）技术，编号相同的根服务器使用同一个IP（类似一个集群）。  

那么为什么只有 13 个 IP 呢？

# 为什么 DNS 根服务器只有 13 个 IP

<pre>
DNS 协议的最初定义要从 20 世纪 80 年代未期开始算起，它使用了 UDP 和 TCP 协议。

UDP 通常用于查询和响应，TCP 用于主服务器和从服务器之间的区域传送。
遗憾的是，在所有 UDP 实现中能保证正常工作的最大包是 512 字节，
对于在每个包中必须含有数字签名的一些 DNS 新特性（例如，DNSSEC）来说实在是太小了。

512 字节的限制还影响了根服务器的数量和名字。

要让所有的根服务器数据能包含在一个 512 字节的 UDP 包中，
根服务器只能限制在 13 个，而每个服务器要使用字母表中的单个字母命名。

以太网数据的长度必须在 46-1500 字节之间，这是由以太网的物理特性决定的。
事实上，这个 1500 字节就是网络层 IP 数据包的长度限制，理论上，IP 数据包最大长度是 65535 字节。

这是由 IP 首部 16 比特总长度所限制的，去除 20 字节 IP 首部和 8 个字节 UDP 首部，
UDP 数据包中数据最大长度为 65507 字节。

在 Internet 数据传输中，UDP 数据长度控制在 576 字节（Internet 标准 MTU 值），
而在许多 UDP 应用程序设计中数据包被限制成 512 字节或更小。这样可以防止数据包的丢失。

许多解析器首先发送一条 UDP 查询，如果它们接收到一条被截断的响应，则会用 TCP 重新发送该查询。
这个过程绕过了 512 字节的限制，但是效率不高。
您或许认为 DNS 应该避开 UDP，总是使用 TCP，但是 TCP 连接的开销大得多。

一次 UDP 名字服务器交换可以短到两个包：一个查询包、一个响应包。
一次 TCP 交换则至少包含 7 个包：三次握手初始化 TCP 会话、一个查询包、一个响应包以及最后一次握手来关闭连接。

</pre>

# Ref
[根域名服务器](https://zh.wikipedia.org/wiki/%E6%A0%B9%E7%B6%B2%E5%9F%9F%E5%90%8D%E7%A8%B1%E4%BC%BA%E6%9C%8D%E5%99%A8)  
[DNS根服务器、根服务器、全球13台根域名服务器、详细介绍](https://www.cloudxns.net/Support/detail/id/1463.html)  