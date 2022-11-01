# blog-site

## 1、站点说明

这是我的博客站点代码。这个站点使用 [Hugo](https://gohugo.io/) 搭建，使用的主题是 [Hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack)。

从这里可以直接访问我的网站 -> [zhuaowei的博客](http://zhuaowei.top/)。博客内容是记录学习过程中的笔记，主要是计算机相关的，后续会逐渐补充更多内容。

## 2、站点部署

站点更新

```shell
git pull origin main
hugo
sudo cp -r public/* /www/root/
```

初次使用

```shell
git clone https://github.com/zhuaowei/blog-site.git mysite
cd mysite
git clone https://github.com/CaiJimmy/hugo-theme-stack.git themes/stack
hugo
sudo cp -r public/* /www/root/
```
