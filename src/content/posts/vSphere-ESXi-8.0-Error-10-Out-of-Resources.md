---
title: 引导 vSphere ESXi 8.0 提示 “Error 10 (Out of Resources)”
published: 2024-09-19
description: '引导 vSphere ESXi 8.0 提示 “Error 10 (Out of Resources)”'
tags: [Esxi,linux,服务器运维,服务器]
category: '教程'
draft: false
---
在传统BIOS引导下，引导ESXi 8.0安装镜像可能会遇到如图报错("Error 10 (Out of resources) while loading module", "Requested malloc size failed", "No free memory".)：

![image-20240919001841501](https://cdn.cnqs.moe/qianshi-cdn/2024/09/21e626d77a896ae22234c27e305b9676.png)

这是因为当硬件机器在传统 BIOS 模式下启动时，只有一小部分内存可供引导加载程序使用。确切的数量取决于机器的硬件配置。如果可用内存太少，启动可能会失败，并显示“Out of resources”或其他类似消息。

进机器BIOS后调整为EFI Boot Type即可 (不同机器可能不同，我这台机器是Huawei RH2288HV2)

![image-20240919002817070](https://cdn.cnqs.moe/qianshi-cdn/2024/09/ce844226a91244e67a511bfd6c049131.png)

按下F10重启机器，并重新引导

![image-20240919003153737](https://cdn.cnqs.moe/qianshi-cdn/2024/09/8956ef1b92c5451b114af5fa432d6cdd.png)

问题解决，本文结束