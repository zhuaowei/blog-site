---
title: "第一次使用Hugo搭建博客"
date: 2022-08-10T21:13:55+08:00
draft: false

tags: ["hugo"]
categories: "hugo"
---

一直想自己搭建一个博客，但是碍于实力与审美，还有自己太懒了，于是就使用Hugo搭建了一个自己的博客。

<!--more-->

**环境：centos7**

下面是具体的折腾流程：

## 一、安装Hugo

**安装Golang**

Hugo是用Go写的，所以首先应该安装Golang。


```
sudo yum -y install golang

# 查看安装的版本
go version

```

**添加yum源**

直接安装找不到安装包，需要先添加yum源。创建下面的文件，然后把内容粘上去。

> sudo vim /etc/yum.repos.d/hugo.repo

```
[daftaupe-hugo]
name=Copr repo for hugo owned by daftaupe
baseurl=https://copr-be.cloud.fedoraproject.org/results/daftaupe/hugo/epel-7-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://copr-be.cloud.fedoraproject.org/results/daftaupe/hugo/pubkey.gpg
repo_gpgcheck=0
enabled=1
```

**安装Hugo**

```
sudo yum install -y hugo
# 查看Hugo的版本
hugo version
```

## 二、创建一个站点

**创建站点**
一个站点就是一个网站，创建好站点后就可以对这个站点进行配置。

```
hugo new site <mysite>
```

**添加主题**

我用的主题是[hugo-paper](https://github.com/nanxiaobei/hugo-paper)，非常简洁，适合展示代码，还有明暗两种主题。

```
# 进入站点根目录
cd mysite
# 下载主题到主题目录
git clone https://github.com/nanxiaobei/hugo-paper.git themes/paper
```

修改配置，配置文件在站点根目录，叫 `config.toml`

```
theme = "paper"
```

还可以复制主题paper文件夹下的配置，根据自己的需要修改配置。

> 配置中的 `baseURL` 是你将来访问站点的URL，一些网页内的页面跳转需要用到，不要写错。

**添加内容**

给站点添加一些文章。

```
hugo new post/first.md
```

这样就创建了一个文章，文章开头 `---` 之间的是文章的信息，不会显示在网页上。

文章内容在最后的 `---` 下面添加。

> 所有的文章都保存在 `content` 目录下，一个 `content` 可以有多个 `section`，也就是多个文件夹，方便管理文章。

**构建**

添加完内容之后就可以生成静态的网页内容了。

```
# 必须在站点的根目录执行。
hugo
```

这样就生成了博客的静态页面，生成的内容放在了 `public` 目录下。

## 三、部署页面

我使用的是 `nginx` 部署的静态页面。修改 `Nginx` 配置：

> sudo vim /usr/local/nginx/conf/nginx.conf

```
# 添加或修改一个server
server {
	listen 80;
	server_name <yoursite.com>;

	# 一会儿你要把生成的内容放在这个目录下
	root <path/to/site>;
	location / {
		index index.html index.htm;
	}
}
```

将 `public` 目录下的内容复制到配置的 `root` 目录下，重新加载Nginx配置 `service nginx reload`。

好了，这个时候访问自己的网站就可以看到博客的内容了。
