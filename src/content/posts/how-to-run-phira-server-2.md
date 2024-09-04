---
title: 如何解决phira-mp服务端编译报错问题
published: 2024-09-04
description: '关于如何解决phira-mp服务端编译报错问题'
tags: [phira-mp,Phira,音游,rust]
category: '教程'
draft: false
---
书接上文：https://cnqs.moe/posts/how-to-run-phira-server/

在 `phira-mp-server/Cargo.toml` 文件的末尾添加

```text
time = "0.3.36"
```

即可通过编译

视频教程:

<iframe width="100%" height="468" src="//player.bilibili.com/player.html?bvid=BV1DBHaeXEwz" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>