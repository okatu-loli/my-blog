---
title: "Phira-mp服务端部署教程"
published: 2023-07-10
tags: [phira-mp,Phira,音游,rust]
category: 教程
draft: false
---

# phira-mp

`phira-mp` 是一个用 Rust 开发的项目，用于支援Phira的多人联机功能。 以下是部署和运行该项目服务端的步骤。

项目的Github仓库（作者不是我）：https://github.com/TeamFlos/phira-mp

::github{repo="TeamFlos/phira-mp"}

本教程配套视频（可供参考）：https://www.bilibili.com/video/BV1pu411j7e5

<iframe width="100%" height="468" src="//player.bilibili.com/player.html?bvid=BV1pu411j7e5" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

简体中文 | [English Version](https://github.com/okatu-loli/phira-mp/blob/main/README.md)

## 环境

*   Rust 1.70 或更高版本

## 服务端安装

### 对于 `Linux` 用户

#### 依赖

首先，如果尚未安装 Rust，请安装。 您可以按照 <https://www.rust-lang.org/tools/install> 中的说明进行操作

对于 Ubuntu 或 Debian 用户，如果尚未安装“curl”，请使用以下命令进行安装：

```shell
sudo apt install curl
```

对于 Fedora 或 CentOS 用户，请使用以下命令：

```shell
sudo yum install curl
```

安装curl后，使用以下命令安装Rust：

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
执行以下命令刷新环境
```shell
source "$HOME/.cargo/env"
```
克隆项目：
```shell
git clone https://ghproxy.com/https://github.com/TeamFlos/phira-mp
cd phira-mp
```

然后，构建项目：
```shell
cargo build --release -p phira-mp-server
```

#### 运行服务端

您可以使用以下命令运行该应用程序：

```shell
RUST_LOG=info target/release/phira-mp-server
```
**TIPS：请注意，启动阶段没有日志，只有玩家成功连接客户端后才有日志**

#### 故障排除

如果遇到与 openssl 相关的问题，请确保安装了 libssl-dev（适用于 Ubuntu 或 Debian）或 openssl-devel（适用于 Fedora 或 CentOS）。 如果问题仍然存在，您可以为编译过程设置 OPENSSL\_DIR 环境变量。

如果您在 Linux 上进行编译并以 Linux 为目标，并收到有关缺少 pkg-config 的消息，则可能需要安装它：

```shell
# 对于 Ubuntu 或 Debian
sudo apt install pkg-config libssl-dev 

# 对于 Fedora 或 CentOS
sudo dnf install pkg-config openssl-devel
```

如果遇到类似`error : linker 'cc' not found`的错误，则可能需要安装gcc编译器：
```shell
# 对于 Ubuntu 或 Debian
sudo apt-get instalt buitd-essential

# 对于 Fedora 或 CentOS
sudo yum groupinstall 'Development Tools'
```
其他问题请参考具体错误信息并相应调整您的环境，或在评论区交流。

### 进程守护（后台保活）
由于会话关闭后会一并关闭当前会话正在运行的进程，如果想要在后台持续运行服务端，我们需要设置进程守护。。以下分别提供了两种常见的解决方案，即`screen`和`systemctl`。
#### （不推荐）screen

Screen 是一个在 Linux 下用来创建、使用和管理多个 shell 会话的全屏窗口管理器。你可以通过 Screen 在后台运行程序，然后在需要时重新连接。

**对于 Ubuntu/Debian 用户**

首先，安装 screen：

```shell
sudo apt install screen
```

创建一个新的 screen 会话并启动你的程序：

```shell
screen -S Phira
```

在此，`Phira` 是你给 screen 会话起的名称，你可以根据实际情况自行设置。

在新的 screen 会话中，启动服务端（注意切换到服务端所在目录）：

```
RUST_LOG=info target/release/phira-mp-server
```

按 `Ctrl+a` 然后 `d` 可以将 screen 会话放到后台运行。如需重新进入会话，可以使用以下指令：
```shell
screen -r Phira
```

**对于 CentOS/Fedora 用户**

首先，安装 screen：

```shell
sudo yum install screen
```

或者如果你在使用 Fedora 或 CentOS 8，你可以使用 dnf：

```shell
sudo dnf install screen
```

后续操作与 Ubuntu/Debian 相同。

#### （推荐）systemctl
由于screen在宿主机关闭并重启后不能重新运行，这里我更推荐`systemctl`

Systemctl 是 Systemd 的主命令，用来管理系统。我们可以创建一个 systemd 服务文件，让我们的应用程序作为一个服务在后台运行。

**对于 Ubuntu/Debian 和 CentOS/Fedora 用户**

首先，创建一个新的 systemd 服务文件：

```shell
sudo nano /etc/systemd/system/phira.service
```

在此，`phira.service` 是给 systemd 服务起的名称，你可以根据实际情况自行设置。

在打开的编辑器中，输入以下内容：

```
[Unit]
Description=running the phira server

[Service]
ExecStart=/root/phira-mp/target/release/phira-mp-server
Environment=RUST_LOG=info
Restart=always
User=root
Group=root
Environment=PATH=/usr/bin:/usr/bin:/usr/local/bin
WorkingDirectory=/root/phira-mp/target/release

[Install]
WantedBy=multi-user.target
```
你需要根据你的实际情况调整和修改目录，具体需要修改这几项：
```
ExecStart 后面的 /root/phira-mp/target/release
WorkingDirectory 后面的 /root/phira-mp/target/release
```
**TIPS: 如果你不知道你的服务端在哪个路径，可以通过`pwd`命令查询**

![image.png](https://cdn.cnqs.moe/qianshi-cdn/2024/09/1cf1f9ec3ce51ff566a4d2c65af8139c.image)

保存并关闭文件。

然后，重新加载 systemd 配置文件：

```shell
sudo systemctl daemon-reload
```

启动你的服务：

```shell
sudo systemctl start phira
```

如果你希望在系统启动时自动启动你的服务，你可以使用以下命令：

```shell
sudo systemctl enable phira
```

如果你需要查看服务状态，可以使用以下命令：

```shell
sudo systemctl status phira
```

### 监控

您可以检查正在运行的进程及其正在侦听的端口：

```shell
ps -aux | grep phira-mp-server
netstat -tuln | grep 12345
```

![image.png](https://cdn.cnqs.moe/qianshi-cdn/2024/09/4bc994523575350836a3ceefaa6ab6d1.image)

## 对于 `Windows` 或 `Android` 用户

请参考官方文档: <https://docs.qq.com/doc/DU1dlekx3U096REdD>
