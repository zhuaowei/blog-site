---
title: "使用云服务器搭建个人图床"
date: 2022-08-13T18:44:50+08:00
draft: false

tags: ["服务器", "图床", "Hugo"]
description: ""
image: "http://upload.zhuaowei.top/api/image/2E0F73FBB49016A3B580C580A2DB4956.png"
---

**前言**

最近使用hugo搭建了一个博客，花了好几天时间，总算搭成了。一开始用的主题莫名其妙编译不通过，后来改用[hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack)，然后开始搭建图床，因为本人博客写的不多，图片用的也少，所以就没买云存储服务。刚开始搭建的Gitee图床，搭完发现Gitee早就对这方面做了限制，然后就用github+jsDelivr，搭完之后发现国内墙早加强了，国内根本无法访问到图片，最后使用自己的服务器搭建的，配合PicGo对图片进行上传。

## 一、配置服务器图床

我在github找到了一个私人图床搭建的项目[ImageBed](https://github.com/Redns/ImageBed)，跟着他的教程一路搭建过来。

需要用到的环境：

- .NET
- Nginx(可选)

### 1.1、安装.NET

```shell
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm

sudo yum install dotnet-sdk-6.0
```
这里直接安装了SDK，不需要的可以只安装runtime。

> 详细安装教程：https://docs.microsoft.com/zh-cn/dotnet/core/install/linux-centos

### 1.2、运行图床工具

下载[图床工具](https://github.com/Redns/ImageBed/releases)，解压后上传到服务器。

进入解压后的目录，运行图床工具

```shell
# 后台运行
nohup dotnet ImageBed.dll &

# 查找工具运行PID
ps -ef | grep dotnet

# 杀死进程
sudo kill <pid>
```

现在就可以通过浏览器访问图床工具的控制台了。`公网IP:12121` 或者 `域名:12121`。（前提是安全组要打开12121端口）

## 二、配置PicGo

### 2.1、安装PicGo

首先找到[PicGo](https://github.com/Molunerfinn/PicGo/releases/tag/v2.3.0)的官方下载页面，选择最新版本v2.3，下载完成就点击安装。安装过程就不多说了，选择一个安装目录就可以了。

### 2.2、安装插件

在左侧找到插件设置，搜索 `imagebed`，安装插件。

![490B671EE571CE7BF48565B4D9702E42.png](http://upload.zhuaowei.top/api/image/490B671EE571CE7BF48565B4D9702E42.png)

### 2.3、配置插件

配置插件非常简单，只需要把自己服务器的URL复制到上面就行了。

例如：`http://公网IP:12121` 或者 `http://域名:12121`

**注意：最后千万不要加上 `/` ，否则会上传失败！！！**

### 2.4、使用图床

点击左侧的上传区，把图片拖进来或者从文件夹选择，上传完成后就会将链接格式直接复制到你的剪贴板，很方便。

最方便的就是搭配Snipaste截图工具，Snipaste会将截图放到剪贴版，直接点击右下角剪贴板，或者将PicGo缩小成一个球，右键选择剪贴板上传（适合上传很多图的情况）。

## 三、配置反向代理

可以配置Nginx反向代理，把上传的URL和端口修改一下，不改也可以用。

Nginx配置只需添加一个server，更改一下域名就可以了，这里更该之后，PicGo也需要更改一下。

```textarea
map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

server {
    listen 80;
    server_name <ip/domain.com>; # 云服务器公网ip (或域名)     

    location / {
        client_max_body_size 100m;                          		# html报文尺寸限制
        
        proxy_pass http://127.0.0.1:12121;

        # Configuration for WebSockets
        proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection $connection_upgrade;
                proxy_cache off;
        # WebSockets were implemented after http/1.0
        proxy_http_version 1.1;

        # Configuration for ServerSentEvents
        proxy_buffering off;

        # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
        proxy_read_timeout 100s;

        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

> 参考：https://github.com/Redns/ImageBed