---
title: "想部署属于自己的Gist服务？来试试OpenGist！"
published: 2023-07-12
tags: [OpenGist,Gist,github]
category: 教程
draft: false
---

# 想部署属于自己的Gist服务？来试试OpenGist！

>🖋️作者： 千石🌟  
>🌐个人网站：cnqs.moe🚀  
>🤝支持方式：💬评论  
>💬欢迎各位在评论区🗣️交流，期待你的灵感碰撞🌈

## 0. 背景
在之前指导学妹Python爬虫编程过程中，我发现代码传递相当不便，直接发送可能会大量占用聊天空间，同时也可能因Python的缩进问题导致错误。尽管我考虑过直接发送文件，但担心学妹可能无法熟练操作。GitHub Gist与Ubuntu的Pastebin服务虽然能解决部分问题，但在国内访问这些平台可能会遇到困难。未找到合适的解决方案，这个问题就悬而未决了。

[<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/069109bd64a949aa9f08fccb64a7cbcc~tplv-k3u1fbpfcp-watermark.image?" alt="image.png" width="50%" />](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/069109bd64a949aa9f08fccb64a7cbcc~tplv-k3u1fbpfcp-watermark.image)

最近在GitHub闲逛的时候，发现了一个自托管的Gist服务项目，很感兴趣，研究了一番，所以有了这篇文章。

## 1. 介绍
### Gist

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/793614864b8b4207a2a5b6052ccd7402~tplv-k3u1fbpfcp-watermark.image?)

`Gist` 是[GitHub](https://zh.wikipedia.org/wiki/GitHub "GitHub")营运的一项 `pastebin` 服务。它允许用户分享代码片段，即 `gists`。`Gist`可以被视为 `GitHub` 仓库，它们可以被 `fork`、`clone` 和修改，就像任何其他类型的 `Git` 仓库一样。

由于本文的重点不在 `Gist` ，感兴趣的小伙伴可以看看这篇文章：
[程序员都应该了解的代码片段管理神器：gist - 掘金 (juejin.cn)](https://juejin.cn/post/6933075278512160776)
### Opengist

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9df344f31154fccbd6fd3f66d0aa459~tplv-k3u1fbpfcp-watermark.image?)

Opengist ：一个由 Git 支持的自托管的 pastebin
- 核心功能与优点：
    - 代码片段的储存与分享
    - 私有代码片段的支持
    - 支持Markdown格式
    - 友好的用户界面
    - 开源的优势

## 2. 部署
作者在 `ghcr.io` 托管了 `Opengist` 的 `docker` 镜像，所以这里推荐使用docker进行部署，方便进行管理

**Tips ：由于 `ghcr.io` 在国内是无法访问的状态，所以我们使用 [hub-mirror](https://github.com/togettoyou/hub-mirror) 这个项目来进行加速**

### Step0：环境准备
安装docker（参考[Docker 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-tutorial.html)）：
```shell
# 对于 Ubuntu 或者 Debian 系统
curl -fsSL https://test.docker.com -o test-docker.sh
sudo sh test-docker.sh
```

```
# 对于 CentOS 系统
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
安装docker compose：
```shell
$ sudo curl -L "https://ghproxy.com/https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
授予权限
```
sudo chmod +x /usr/local/bin/docker-compose
```
创建软链
```
$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
测试是否安装成功
```
$ docker-compose version
```

**Tips：`Windows 和 Mac` 的 Docker 桌面版和 Docker Toolbox 已经包括 Compose 和其他 Docker 应用程序，因此 `Windows 和 Mac` 用户不需要单独安装 Compose。**

对于 `加速镜像` 和 `Windows` 下 `docker` 的安装，可以参考下面两个链接：
- [Windows Docker 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/windows-docker-install.html)
- [Docker 镜像加速 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-mirror-acceleration.html)
### Step1：拉取镜像
执行下面的指令：

```shell
echo -e '	\ndocker pull togettoyou/ghcr.io.thomiceli.opengist:latest\ndocker tag togettoyou/ghcr.io.thomiceli.opengist:latest ghcr.io/thomiceli/opengist:latest\n\n' | bash
```
### Step2：编写`docker-compose.yml`
创建一个名为 `docker-compose.yml` 的文件，填入以下内容，并根据注释内容结合自身需求自行修改：
```yml
version: "3"

services:
  opengist:
    image: ghcr.io/thomiceli/opengist:1.4
    container_name: opengist
    restart: unless-stopped # 官方默认没有开机自启，如果你需要开机自启，unless-stopped可以改为always
    ports:
      - "6157:6157" # HTTP 端口
      - "2222:2222" # SSH 端口，如果不需要ssh可以去掉
    volumes:
      - "$HOME/.opengist:/opengist"
```
### Step3：运行
`docker-compose.yml` 同目录下执行命令：
```
docker-compose up -d
```
通过以下命令可以查看运行状态：
```
docker ps
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a562fabbc20405c9b82085149c3c1b3~tplv-k3u1fbpfcp-watermark.image?)

Opengist 现在正在6157端口运行，你可以通过 [http://localhost:6157](http://localhost:6157/) 来浏览。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/80abe2c39b694341aedab6d16b8f13b0~tplv-k3u1fbpfcp-watermark.image?)

## 3. 使用
Opengist 第一次创建没有任何账号，需要自行创建

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6c1f34a8825a417c88619a0df3ede9f2~tplv-k3u1fbpfcp-watermark.image?)

注册并登录后默认是管理员账号

下面这张图囊括了 Opengist 的所有操作：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/acc8d3833d0a4e1a80f099f32137f51e~tplv-k3u1fbpfcp-watermark.image?)

## 最后
Opengist使用起来还是很简单的，虽然没有中文支持，但是界面简单、上手难度不大，学习成本很低，多熟悉几次小白也能轻松入门，感兴趣并且有需求的小伙伴可以去尝试下~
