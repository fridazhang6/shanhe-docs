---
title: "公网IP网络延迟增大"
description: test
weight: 5
draft: false
keyword: QingCLoud, 青云, 公网IP, 弹性EIP, 
---

公网 IP 作为青云平台各个资源访问互联网的桥梁，经常会出现通过云服务器访问互联网或者互联网访问云服务响应慢，有延迟的情况，本文将介绍如何处理云服务器公网 IP 网络延迟大的情况。

## 1.确认公网IP的带宽上限

用户申请公网IP都是有带宽上限的，我们可以在控制台界面进行查看公网IP的带宽上限

![high_ping](../../_images/high_ping1.png)

可以看到，这个公网IP的带宽上限为10Mbps。

## 2.查看网络延迟

正常情况下，我们在互联网对一个公网IP的延迟，可以通过ping来进行测试
```bash
#ping 139.198.188.188
正在 Ping 139.198.188.187 具有 32 字节的数据:
来自 139.198.188.187 的回复: 字节=32 时间=39ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=39ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=39ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=39ms TTL=52
```

保持一段时间的输出内容，只要时间这一列不超过100ms，且没有太大的波动，位置在某个范围内，就说明客户端到青云公网IP的网络环境比较稳定。

## 3.延迟大的原因

当出现通过云服务器访问互联网或者互联网访问云服务响应慢时，通常有如下几个原因：我们正在上传文件到云服务器或者在云服务器从互联网下载数据；我们对外提供的业务，有大量流量进出（既有用户在访问您的业务）等原因。

我们现在以上传文件到云服务器的场景为例：

假如现在遇到了访问云服务器对外提供的WEB服务卡顿的问题，我们通过ping来检测下客户端到云服务器的网络延迟
```bash
#ping 139.198.188.188 -t
正在 Ping 139.198.188.187 具有 32 字节的数据:
来自 139.198.188.187 的回复: 字节=32 时间=163ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=145ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=145ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=138ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=154ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=143ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=139ms TTL=52
来自 139.198.188.187 的回复: 字节=32 时间=139ms TTL=52

139.198.188.188 的 Ping 统计信息:
    数据包: 已发送 = 8，已接收 = 8，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 138ms，最长 =163ms，平均 = 145ms
```

我们发现延迟在150ms左右，我们进入控制台看下公网IP的带宽监控

![high_ping](../../_images/high_ping2.png)

通过监控我们可以看到有较高的进带宽，已经将10Mbps的带宽都占满了，此时我们为了快速恢复WEB服务器的访问，需要提高公网IP的带宽上限，来尽快完成数据的传输，等数据传输完毕，再将带宽调成原来的10Mbps即可。

![high_ping](../../_images/high_ping3.png)

>**说明**
>
>如果没有下载或者上传，但是也出现了较高的带宽占用，这个很有可能是有大量的业务访问，此时需要排查下业务层的日志，看下是不是正常的业务流量，如果是恶意攻击，可以在云服务器绑定的安全组对应端口添加一个优先级较高的拒绝规则，源IP填写日志中恶意访问业务的客户端IP，然后需要重启下业务来断开之前已经建立的连接。