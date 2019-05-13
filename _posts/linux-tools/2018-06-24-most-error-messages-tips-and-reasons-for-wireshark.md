---
layout: post
title: Wireshark常见报文错误提示和原因
category: [linux-tools]
author: Howie Lee
tags: [Linux,Wireshark,网络抓包]
---

## 前言
Wireshark 抓取的网络包经过分析后，会根据每个数据包的信息进行分类(分级)，并给出颜色。
对于错误包会使用黑底红字重点突出出来。  
由于TCP(Transmission Control Protocol 传输控制协议)一种面向连接的、可靠的、基于字节流的传输层通信协议,这些错误提示很多是TCP协议可以自己修复的，但是也不排除某些错误提示是致命性错误，了解这些错误提示的具体意义和产生原因就变得至关重要。

## 错误类型和原因
### TCP previous segment lost (tcp先前的分片丢失）

到达分组中的序列号高于下一期望的序列号，指示至少一个段被丢弃/丢失。接收站通过针对其接收的每个附加分组发送重复ACK来纠正这种情况，直到发送方重传丢失的分组为止。

### TCP acked lost segment（tcp应答丢失）
发送方发送了报文之后等待接收方给出ACK应答，在规定的等待时间内没有收到接收方的ACK会提示该信息。

### TCP window update（tcp窗口更新）
TCP Window Update 是TCP通信中的一个状态，它可以发生的原因有很多，但最终归结于发送者传输数据的速度比接收者读取的数据还快，这使得接收端的在缓冲区必须释放一部分空间来装发送过来的数据，然后向发送者发送Windows Update，告诉给发送者应该以多大的速度发送数据，从而使得数据传输与接受恢复正常。
### TCP dup ack（tcp重复应答）

Tcp Dup Ack xxx#y 代表了数据段丢失TCP状态，xxx代表数据丢失的位置，#后代表第几次丢失文。

一般来说是网络拥塞导致丢包，比如发送方的报文到达不了接收方，接受方收不到预期序列号的报文就会发送dup ack给发送方，发送方收到3个dup ack就会快速重传而不必等超时定时器。
### TCP keep alive（tcp保持活动）
Keep-alive的机制可以检测死连接，TCP会在空闲了一定时间后发送数据给对方：

1. 如果主机可达，对方就会响应ACK应答，就认为是存活的。

2. 如果可达，但应用程序退出，对方就发RST应答，发送TCP撤消连接。

3. 如果可达，但应用程序崩溃，对方就发FIN消息。

4. 如果对方主机不响应ack, rst，继续发送直到超时，就撤消连接。这个时间默认是2小时。

### TCP retransmission（tcp重传）
### TCP port numbers reused（tcp端口重复使用）

TCP协议中规定，处在 TIME-WAIT 状态的TCP端，必须等待一定时长（协议建议2MSL），尽量确保TCP远端能够收到最后一次FIN分段的ACK确认。处于TIME-WAIT状态的TCP端，不能重建以同一四元组标识的TCP替身连接。该提示是由于客服端重新建立连接的端口号使用了仍然处在2MSL等待时长内的端口号

### TCP fast retransmission (tcp快速重传)

TCP层检测到拥塞发生一般是两个条件之一，超时或者dup ack。

dup ack三次之后就进入快速重传/快速恢复 or 快速重传/慢启动

### TCP spurious retransmission(tcp伪重传)
该提示是由于发送端重发了一个已经收到应答的报文段导致。

### TCP segment of a reassembled PDU (重新组装的PDU的TCP片段)

TCP层收到上层大块报文后分解成段后发出去。

收到一个报文后如何确定它是一个"TCP segment"？如果有几个报文的ACK序号都一样，并且这些报文的Sequence Number都不一样，并且后一个Sequence Number为前一个Sequence Number加上前一个报文大小再加上1的话，肯定是TCP segment了，对于没有ACK标志时，则无法判断。

## 参考
1. [Spurious Retransmissions](https://blog.packet-foo.com/2013/06/spurious-retransmissions/comment-page-1/)
2. [Wireshark抓包常见问题解析](http://www.xianren.org/net/wireshark-q.html)
3. [维基百科：TCP/IP协议族](https://zh.wikipedia.org/wiki/TCP/IP%E5%8D%8F%E8%AE%AE%E6%97%8F)