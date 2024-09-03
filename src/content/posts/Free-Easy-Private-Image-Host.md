---
title: "零成本！无需服务器，建立属于你的图床"
published: 2023-07-20
tags: [自建图床,Cloudflare,Typora,PicGO]
category: 教程
draft: false
---

## 背景
博客的图片之前一直托管在某平台，由于平台操作失误导致部分图片失联，遂决定自建图床并将图片托管在图床上。

在互联网上搜索到了一些方案，结合自身需求（低成本、方便高效、不需要对现有的写作方案做太多改动），遂选择了 `Clodflare R2 + PicGo`  的组合。

## 方案优势
1. **稳定性强**：相对于之前依赖的第三方平台，自建图床为我可以拥有更加稳定和可靠的服务。这确保了我的图片始终可访问，避免了因外部原因导致的图片失联。

2. **高度自定义**：利用 `Clodflare R2` 和 `PicGo`，我可以根据自己的需求自由地调整存储、上传和分享等功能，能更灵活的进行图片管理。

3. **与Typora集成**： `Typora` 官方在某一次更新中支持了 `PicGo` ，通过简单的设置就能实现一键上传图片，大大简化了图片的插入过程。而`OneDrive` 的同步功能保障了我的图片数据得到双重备份和安全存储。

4. **零成本高效**：这套方案无需任何额外的费用，同时为我提供了高效、稳定和安全的图片托管服务。

5. **数据安全和隐私保护**：通过自建图床，我对自己的数据拥有完全的掌控，避免了与第三方服务分享数据的风险。

6. **易于拓展和升级**：随着我的个人需求的变化，我可以在这个方案的基础上轻松进行拓展或升级，利于扩展和自定义。

## 介绍
这里我对上面提到的平台和软件进行介绍
### Cloudflare R2
Cloudflare R2是Cloudflare于2021年推出的对象存储服务

![Cloudflare R2首页](https://cdn.cnqs.moe/qianshi-cdn/2023/07/cbadb6d3773dc73cab41c02c8f053134.png)

**优势**：
- 每月有`10G`免费额度，对于个人站长来说足够使用
- 定价低，能省下一笔成本
- 支持S3兼容API，可以方便的和其他应用集成（比如 `PicGo` ）
- 提供了灵活的按需付费模式，没有最小费用要求
- 域名无需备案，接入 `Cloudflare` 即可

**定价**：

![Cloudflare R2 产品定价](https://cdn.cnqs.moe/qianshi-cdn/2023/07/27d86791e6c0d88c6378fb24b9ab4f21.png)

详细定价：[点击链接](https://developers.cloudflare.com/r2/pricing/	"Cloudflare R2 详细定价")

### PicGo
PicGo是一个支持多样化图片上传需求的跨平台开源图床工具，它可以方便地上传图片并获取图片URL,无需手动操作。

![image-20230720141117821](https://cdn.cnqs.moe/qianshi-cdn/2023/07/32f57405f983c70ac128866fa5097e68.png)


### Typroa
Typora是一款简洁强大的Markdown编辑器,支持实时预览、自定义样式、多平台等特性,是编写Markdown文档的优秀工具选择。

![image-20230720141757258](https://cdn.cnqs.moe/qianshi-cdn/2023/07/6e7eb070308027079df0a9227f9d4d58.png)

## 步骤
接下来开始介绍具体步骤
### 开通Cloudflare R2

进入官网：[Cloudflare 中国官网](https://www.cloudflare.com/zh-cn/)，点击官网右上角的登录，按照流程登录

![image-20230720142018039](https://cdn.cnqs.moe/qianshi-cdn/2023/07/7a3099bd97fc6626dc2a9fa8ac504f06.png)

点击导航栏的 R2 按钮

![image-20230720142356287](https://cdn.cnqs.moe/qianshi-cdn/2023/07/4089d48e7535f932cf8952c38d588974.png)

按照提示开通服务，并点击 `创建存储桶`

**Tips**：这一步可能需要验证信用卡或者 `PayPal` 验证，由于我绑定过 `PayPal` 所以没有验证，知道的小伙伴可以在评论区补充

![image-20230720142505837](https://cdn.cnqs.moe/qianshi-cdn/2023/07/5160728015d814635fe4a6f73ff339d8.png)

填写相关资料（名称需是英文）

![image-20230720142904142](https://cdn.cnqs.moe/qianshi-cdn/2023/07/e379cf5ecddad1d90ee5968ae385f0da.png)

点击 `提供位置提示` 可以手动选择地区

![image-20230720143022186](https://cdn.cnqs.moe/qianshi-cdn/2023/07/5e82295bef2083569d1427bd73bfbeab.png)

点击右下角的创建存储桶，即创建成功

![image-20230720143725582](https://cdn.cnqs.moe/qianshi-cdn/2023/07/069c992c18ac0a91fe1e60f583b92cde.png)

点击 `连接域` 可以设置自定义域名（可选）

![image-20230720151714447](https://cdn.cnqs.moe/qianshi-cdn/2023/07/da35a855be30fe81c3dc28012e46b28c.png)

返回 `R2` 首页，点击 `管理 R2 API 令牌`，随后点击 `创建API令牌`

![image-20230720150245559](https://cdn.cnqs.moe/qianshi-cdn/2023/07/d27863c33e71c1bada05a3f16f494269.png)

![image-20230720150029283](https://cdn.cnqs.moe/qianshi-cdn/2023/07/ec39fc5b99e9b7ef8333113db908dcc5.png)

这里务必选中 `编辑` 选项

![image-20230720150408925](https://cdn.cnqs.moe/qianshi-cdn/2023/07/e4ed1e2df92253f8de8f9ce68c9851be.png)

记住这里的 `密匙`，后面会用到

![image-20230720150604331](https://cdn.cnqs.moe/qianshi-cdn/2023/07/85ff536c4ed729943027f94405ddb973.png)

### 配置PicGo

由于我的主力机系统是 `Windows` ，所以下面的内容都是基于 `Windows` 环境编写，其它系统请自行探索（但其实差别不大）

下载PicGo：https://ghproxy.com/https://github.com/Molunerfinn/PicGo/releases/download/v2.3.1/PicGo-Setup-2.3.1-x64.exe

按流程安装即可，打开后点击 `插件设置`，搜索 `s3`，安装即可：

![image-20230720145154542](https://cdn.cnqs.moe/qianshi-cdn/2023/07/cef2325f5fd757e322b712c6145d6770.png)

安装成功后，依次点击 `图床设置` 、`Amazon S3` 

![image-20230720145707371](https://cdn.cnqs.moe/qianshi-cdn/2023/07/aef6682828e35311130741d95407b947.png)

![image-20230720151234052](https://cdn.cnqs.moe/qianshi-cdn/2023/07/2fb57b6a82bc59a1bb7e79b22029f5ba.png)

- 应用密钥 ID/应用密钥：之前获取的ID和密钥
- 桶名：最开始设置的桶名称
- 自定义节点（红色覆盖处）：![image-20230720151434859](https://cdn.cnqs.moe/qianshi-cdn/2023/07/0d2af19ac4efaf81edb69143c958c57a.png)
- 自定义域名：按需填写即可

点击 `确定`

### 设置 Typora

同时按下键盘上的 `Ctrl` 和 `,` 按键，应该会跳转至 `typora` 的设置页面

![image-20230725182206300](https://cdn.cnqs.moe/qianshi-cdn/2023/07/1f05f414a2558e54cb79c417be0721db.png)

选择 `PicGo` ，并填入picgo的相关路径即可

![image-20230725182653229](https://cdn.cnqs.moe/qianshi-cdn/2023/07/8eb53413c6e8e9653532d1fd696c9d5b.png)

之后你在typora粘贴文件时会自动上传文件到Cloudflare R2
