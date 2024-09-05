---
title: OpenCloudOS 8 下部署学之思开源考试系统
published: 2024-07-25
description: 'OpenCloudOS 8 下部署学之思开源考试系统'
tags: [学之思开源考试系统,OpenCloudOS 8,MySQL,Vue,Java]
category: '教程'
draft: false
---
## 前言

接到任务，需要部署“[学之思开源考试系统](https://github.com/mindskip/xzs-mysql "学之思开源考试系统")”，恰巧这次部署的服务器的系统是腾讯开源的OpenCloudOS 8，网络上关于这个系统部署项目的文档还比较少，遂作一次记录

本文默认读者具有基本的Linux操作知识，如果不了解这部分内容请自行查阅相关资料或者询问AI，文章不做过多说明

## 正文

在之前参加腾讯的一个开源活动中，我深入体验了OpenCloudOS系统，并且在其上进行[开发和参与开源项目](https://cnqs.moe/honor/#%E8%85%BE%E8%AE%AFOpenCloudOS%E5%B9%B4%E5%BA%A6%E4%BC%98%E7%A7%80%E8%B4%A1%E7%8C%AE%E8%80%85 "开发和参与开源项目")。因此，这次在部署过程中，我感到比较得心应手。

### 环境搭建

#### 1.nodejs安装

由于官方软件仓库自带的nodejs版本过老，无法满足项目要求，遂这次选择下载官方的 Node.js 二进制发行版进行安装
附上官方仓库的nodejs版本情况：

```bash
dnf search nodejs
```

输出：

```bash
[root@VM-16-15-opencloudos xzs]# dnf search nodejs
Last metadata expiration check: 0:20:28 ago on Wed 24 Jul 2024 08:41:34 PM CST.
====================================================================== Name Exactly Matched: nodejs =======================================================================nodejs.x86_64 : JavaScript runtime
===================================================================== Name & Summary Matched: nodejs ======================================================================nodejs-GeographicLib.noarch : NodeJS implementation of GeographicLib
========================================================================== Name Matched: nodejs ===========================================================================nodejs-codemirror.noarch : A versatile JS text editor
nodejs-devel.x86_64 : JavaScript runtime - development headers
nodejs-docs.noarch : Node.js API documentation
nodejs-full-i18n.x86_64 : Non-English locale data for Node.js
nodejs-less.noarch : Less.js The dynamic stylesheet language
nodejs-marked.noarch : A markdown parser for JavaScript built for speed
nodejs-nodemon.noarch : Simple monitor script for use during development of a node.js app
nodejs-packaging.noarch : RPM Macros and Utilities for Node.js Packaging
========================================================================= Summary Matched: nodejs =========================================================================cjdns-tools.noarch : Nodejs tools for cjdns
```

首先，访问 Node.js 官方网站，找到并下载 Node.js 二进制发行版，这里我选择`v16`版本，可以在`nodejs`官网的[这个页面](https://nodejs.org/zh-cn/about/previous-releases "这个页面")找到你需要的所寻找`Node.js`版本最新的二进制发行版

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6e526b42b5e24c2795db03e5944a2239~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgQ07ljYPnn7M=:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMzg0MzU0ODM4MzgxNjg2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1726128009&x-orig-sign=mNaaA3GY45DJHo2N0gOamV7YjG4%3D)

这里根据需求使用`wget`下载或者本地下载后手动传到服务器，这里使用`wget`下载

```bash
wget https://nodejs.org/download/release/v16.20.2/node-v16.20.2-linux-x64.tar.gz
```

下载完成后新建目录并将下载得到的压缩包放进新建的目录中，防止污染服务器环境

```bash
mkdir node
mv node-v16.20.2-linux-x64.tar.gz node
cd node
ls
```

输出

```bash
[root@VM-16-15-opencloudos ~]# mkdir node
[root@VM-16-15-opencloudos ~]# mv node-v16.20.2-linux-x64.tar.gz node
[root@VM-16-15-opencloudos ~]# cd node
[root@VM-16-15-opencloudos node]# ls
node-v16.20.2-linux-x64.tar.gz
[root@VM-16-15-opencloudos node]# 
```

使用 tar 命令解压下载的压缩包，并查看目录情况：

```bash
tar -xzf node-v16.20.2-linux-x64.tar.gz
ls
```

输出：

```bash
[root@VM-16-15-opencloudos node]# tar -xzf node-v16.20.2-linux-x64.tar.gz
[root@VM-16-15-opencloudos node]# ls
node-v16.20.2-linux-x64  node-v16.20.2-linux-x64.tar.gz
```

进入并检查解压后的文件夹，无误后将解压后的文件移动到到`/usr/local` 目录下（或者你选择的其他目录）：

```bash
cd node-v16.20.2-linux-x64/
ls -al
cd ..
sudo mv node-v16.20.2-linux-x64 /usr/local/node-v16.20.2
ls /usr/local/node-v16.20.2
```

输出：

```bash
	[root@VM-16-15-opencloudos node]# cd node-v16.20.2-linux-x64/
	[root@VM-16-15-opencloudos node-v16.20.2-linux-x64]# ls -al
	total 856
	drwxr-xr-x 6 1001 1001   4096 Aug  9  2023 .
	drwxr-xr-x 3 root root   4096 Jul 24 21:25 ..
	drwxr-xr-x 2 1001 1001   4096 Aug  9  2023 bin
	-rw-r--r-- 1 1001 1001 720035 Aug  9  2023 CHANGELOG.md
	drwxr-xr-x 3 1001 1001   4096 Aug  9  2023 include
	drwxr-xr-x 3 1001 1001   4096 Aug  9  2023 lib
	-rw-r--r-- 1 1001 1001  92799 Aug  9  2023 LICENSE
	-rw-r--r-- 1 1001 1001  35345 Aug  9  2023 README.md
	drwxr-xr-x 5 1001 1001   4096 Aug  9  2023 share
	[root@VM-16-15-opencloudos node-v16.20.2-linux-x64]# cd ..
	[root@VM-16-15-opencloudos node]# sudo mv node-v16.20.2-linux-x64 /usr/local/node-v16.20.2
	[root@VM-16-15-opencloudos node]# ls /usr/local/node-v16.20.2
	bin  CHANGELOG.md  include  lib  LICENSE  README.md  share
```

为了方便使用 node 和 npm 命令，创建符号链接：

```bash
sudo ln -s /usr/local/node-v16.20.2/bin/node /usr/bin/node
sudo ln -s /usr/local/node-v16.20.2/bin/npm /usr/bin/npm
```

最后，验证 Node.js 和 npm 是否正确安装：

```bash
node -v
npm -v
```

输出：

```bash
	[root@VM-16-15-opencloudos node]# node -v
	v16.20.2
	[root@VM-16-15-opencloudos node]# npm -v
	8.19.4
```

（可选）当然，也可以通过配置环境变量来使用 node 和 npm 命令。编辑你的 shell 配置文件（例如 \~/.bashrc 或 \~/.bash\_profile），添加以下行，然后，重新加载配置文件，这里不作详细演示，可以参考下面的命令：

```bash
# 需要添加的行
export PATH=/usr/local/node-v16.20.2/bin:$PATH
# 添加行后需要执行的命令
source ~/.bashrc
```

#### 2.安装Java和Maven

这里用到的Java版本是Java1.8，使用官方软件仓库的版本即可：

```bash
dnf install java-1.8.0-openjdk.x86_64 maven -y
```

安装完成查看Java版本状态:

```bash
java -version
mvn -version
```

输出:

```bash
[root@VM-16-15-opencloudos node]# java -version
openjdk version "1.8.0_412"
OpenJDK Runtime Environment (build 1.8.0_412-b08)
OpenJDK 64-Bit Server VM (build 25.412-b08, mixed mode)
```

#### 3.安装MySQL

可以参考[官方文档](https://docs.opencloudos.org/OCS/Typical_Application_Deployment/MySQL_guide/ "官方文档")进行部署，这里不多赘述

#### 4.安装nginx

```bash
sudo dnf install nginx
```

验证安装：

```bash
nginx -v
```

输出:

```bash
[root@VM-16-15-opencloudos xzs]# nginx -v
nginx version: nginx/1.14.1
```

到此，环境配置完成

### 项目部署

这里，参考[官方文档](https://www.mindskip.net:999/guide/deploy.html "官方文档")进行部署
首先进入`/opt`目录，克隆官方仓库源码

```bash
cd /opt
git clone https://gitee.com/mindskip/xzs-mysql.git
```

如果系统提示未找到git命令可以用下面的命令进行git安装，再执行上面的操作:

```bash
dnf install git -y
```

#### 前端打包

分别在\source\vue\xzs-student目录和source\vue\xzs-admin目录，执行前端打包命令

```bash
npm install --registry https://registry.npmmirror.com
npm run build
```

打包后的目录为student和admin
先创建/usr/local/xzs/web/目录，然后将打包后的student、admin放到此目录下

```bash
mkdir -p /usr/local/xzs/web/
mv /opt/xzs/xzs-mysql/source/vue/xzs-admin/admin /usr/local/xzs/web/
mv /opt/xzs/xzs-mysql/source/vue/xzs-admin/student /usr/local/xzs/web/
```

#### 后端打包

修改`/opt/xzs/xzs-mysql/source/xzs/src/main/resources`目录下`application-prod.yml`中的datasource地址、数据库名以及数据库的用户名密码

```bash
[root@VM-16-15-opencloudos resources]# cat application-prod.yml 
logging:
  path: /usr/log/xzs/

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/数据库名?useSSL=false&useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&allowPublicKeyRetrieval=true&allowMultiQueries=true
    username: 数据库用户名
    password: '数据库密码'
    driver-class-name: com.mysql.cj.jdbc.Driver
```

跳转到`/opt/xzs/xzs-mysql/source/xzs`目录，执行以下命令打包：

```bash
cd /opt/xzs/xzs-mysql/source/xzs
mvn clean package
```

看到以下类似输出(BUILD SUCCESS)说明项目打包成功：

    [root@VM-16-15-opencloudos xzs]# mvn clean package
    [INFO] Scanning for projects...
    [INFO] 
    [INFO] --------------------------< com.mindskip:xzs >--------------------------
    [INFO] Building xzs 3.9.0
    [INFO] --------------------------------[ jar ]---------------------------------
    [INFO] 
    [INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ xzs ---
    [INFO] 
    [INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ xzs ---
    [INFO] Using 'UTF-8' encoding to copy filtered resources.
    [INFO] Copying 6 resources
    [INFO] Copying 14 resources
    [INFO] 
    [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ xzs ---
    [INFO] Changes detected - recompiling the module!
    [INFO] Compiling 197 source files to /opt/xzs/xzs-mysql/source/xzs/target/classes
    [INFO] 
    [INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ xzs ---
    [INFO] Using 'UTF-8' encoding to copy filtered resources.
    [INFO] skip non existing resourceDirectory /opt/xzs/xzs-mysql/source/xzs/src/test/resources
    [INFO] 
    [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ xzs ---
    [INFO] No sources to compile
    [INFO] 
    [INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ xzs ---
    [INFO] Tests are skipped.
    [INFO] 
    [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ xzs ---
    [INFO] Building jar: /opt/xzs/xzs-mysql/source/xzs/target/xzs-3.9.0.jar
    [INFO] 
    [INFO] --- spring-boot-maven-plugin:2.1.6.RELEASE:repackage (repackage) @ xzs ---
    [INFO] Replacing main artifact with repackaged archive
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 3.530 s
    [INFO] Finished at: 2024-07-25T14:10:52+08:00
    [INFO] ------------------------------------------------------------------------

#### 前后端分离部署

在`/opt/xzs/xzs-mysql/source/xzs`目录下，执行以下命令启动后端程序:

```bash
java -Duser.timezone=Asia/Shanghai -jar -Dspring.profiles.active=prod  ./target/xzs-3.9.0.jar  > start1.log  2>&1 &
```

查看进程和后端日志，无误则正常启动:

```bash
# 查看进程日志
ps -ef | grep java
```

输出

```bash
# 可以看到xzs-3.9.0.jar已经正常运行
[root@VM-16-15-opencloudos xzs]# ps -ef | grep java
root      117081       1  0 00:19 ?        00:00:38 /usr/bin/java -Duser.timezone=Asia/Shanghai -jar -Dspring.profiles.active=prod ./target/xzs-3.9.0.jar
root      468550  447951  0 14:31 pts/0    00:00:00 grep --color=auto java
```

进行下一步之前，你需要确定网站的域名已经成功绑定到你的服务器IP，并且服务器防火墙已经打开80和8000端口，如果有一步没有完成可能会导致你的网站无法正常打开/运行，这里可以在网上搜索对应教程，本文不过多赘述
编辑`/etc/nginx/nginx.conf`，添加以下内容(注意根据你的需要进行修改，可以借助ai)：

```bash
server {
    server_name  xzs.example.com;

    # 根目录设置为 /usr/local/xzs/web/student
    root /usr/local/xzs/web/student;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass  http://localhost:8000;
    }

    error_page 404 /404.html;
    location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
}

# 配置 xzs-admin.xzs.example.com
server {
    server_name  xzs-admin.xzs.example.com;

    # 根目录设置为 /usr/local/xzs/web/student
    root /usr/local/xzs/web/admin;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass  http://localhost:8000;
    }

    error_page 404 /404.html;
    location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
}
```

完整文件参考（已去敏）：

```bash
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        #    root /usr/local/xzs/web/;
            index index.html;
        }
        #location /api/ {
        #    proxy_pass  http://localhost:8000;
        #}

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
    # 配置 xzs.example.com
server {
    server_name  xzs.example.com;

    # 根目录设置为 /usr/local/xzs/web/student
    root /usr/local/xzs/web/student;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass  http://localhost:8000;
    }

    error_page 404 /404.html;
    location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
}

# 配置 xzs-admin.example.com
server {
    server_name  xzs-admin.example.com;

    # 根目录设置为 /usr/local/xzs/web/student
    root /usr/local/xzs/web/admin;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass  http://localhost:8000;
    }

    error_page 404 /404.html;
    location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }

}
```

保存并退出后，可以通过`nginx -t`命令检测配置文件是否正确：

```bash
nginx -t
```

输出:

```bash
[root@VM-16-15-opencloudos nginx]# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

重启并查看nginx状态：

```bash
systemctl restart nginx && systemctl status nginx
```

输出

```bash
[root@VM-16-15-opencloudos nginx]# systemctl restart nginx && systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2024-07-25 14:27:47 CST; 10ms ago
  Process: 465333 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 465319 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 465316 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 465343 (nginx)
    Tasks: 5 (limit: 49021)
   Memory: 8.9M
   CGroup: /system.slice/nginx.service
           ├─465343 nginx: master process /usr/sbin/nginx
           ├─465344 nginx: worker process
           ├─465346 nginx: worker process
           ├─465348 nginx: worker process
           └─465349 nginx: worker process

Jul 25 14:27:46 VM-16-15-opencloudos systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jul 25 14:27:46 VM-16-15-opencloudos nginx[465319]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jul 25 14:27:46 VM-16-15-opencloudos nginx[465319]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jul 25 14:27:47 VM-16-15-opencloudos systemd[1]: Started The nginx HTTP and reverse proxy server.
```

到这里，可以分别访问xzs.example.com和xzs-admin.example.com进行测试，没意外，可以访问到对应页面,如果无法访问，请检查以上步骤是否有遗漏

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/79ee6e6e46b84a6ab3456d5260f472b3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgQ07ljYPnn7M=:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMzg0MzU0ODM4MzgxNjg2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1726128009&x-orig-sign=IOsoxDfW5x7hoow8dE%2FCIBxJeK8%3D)


![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/fdc9ce52203f4109b850ef4b63424fa6~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgQ07ljYPnn7M=:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMzg0MzU0ODM4MzgxNjg2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1726128009&x-orig-sign=KzIvm2brBHx1QrsDByF7HzhZJtQ%3D)

#### 部署ssl

执行以下命令可以一键部署来自`Let's Encrypt`的免费ssl证书

```bash
sudo dnf install epel-release -y
sudo dnf install certbot -y
sudo dnf install python3-certbot-nginx -y
sudo certbot --nginx -d xzs.example.com -d xzs-admin.example.com
```

#### 守护进程
更方便地启动、停止和重启应用程序，也为了保证项目的高可用，最好加上进程守护
首先编辑服务文件
```bash
sudo vi /etc/systemd/system/xzs.service
```
在服务文件中添加以下内容：
```bash
[Unit]
Description=XZS Java Application
After=network.target

[Service]
User=root
WorkingDirectory=/path/to/your/application
ExecStart=/usr/bin/java -Duser.timezone=Asia/Shanghai -jar -Dspring.profiles.active=prod ./target/xzs-3.9.0.jar
SuccessExitStatus=143
StandardOutput=file:/path/to/your/application/start1.log
StandardError=file:/path/to/your/application/start1.log
Restart=always

[Install]
WantedBy=multi-user.target
```
确保以下几点：

- User= 行指定运行该服务的用户。
- WorkingDirectory= 行指定你的应用程序所在的目录。
- ExecStart= 行指定启动命令。
- StandardOutput= 和 StandardError= 行指定日志文件的路径。

重新加载 systemd 配置：
```bash
sudo systemctl daemon-reload
```
启动服务：
```bash
sudo systemctl start xzs.service
```
检查服务状态：
```bash
sudo systemctl status xzs.service
```
如果服务启动成功，你应该会看到类似以下的输出：
```bash
[root@VM-16-15-opencloudos xzs]# sudo systemctl status xzs.service
● xzs.service - XZS Java Application
   Loaded: loaded (/etc/systemd/system/xzs.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2024-07-25 00:19:23 CST; 14h ago
 Main PID: 117081 (java)
    Tasks: 220 (limit: 49021)
   Memory: 541.8M
   CGroup: /system.slice/xzs.service
           └─117081 /usr/bin/java -Duser.timezone=Asia/Shanghai -jar -Dspring.profiles.active=prod ./target/xzs-3.9.0.jar

Jul 25 00:19:23 VM-16-15-opencloudos systemd[1]: Started XZS Java Application.
```
启用服务，使其在系统启动时自动启动：
```bash
sudo systemctl enable xzs.service
```
停止服务：
```bash
sudo systemctl stop xzs.service
```
重启服务：
```bash
sudo systemctl restart xzs.service
```

至此，项目部署完成
