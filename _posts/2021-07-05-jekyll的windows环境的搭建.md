---
layout:     post
title:      jekyll的windows环境的搭建
subtitle:   jekyll的windows环境的搭建
date:       2021-07-05
author:     zhaoguangnan
header-img: img/about_cpp_study.jpg
catalog: true
tags:
    - jekyll
---

# 1、jekyll在window下环境的搭建
## 1.1、搭建jekyll环境的目的

​	方便在window平台本地调试**github page**的网站主页的文章显示的效果，没必要每次都提交主页到github上面再等一段时间去看。

## 1.2、安装Ruby

​	打开[ruby的官方下载网址](https://rubyinstaller.org/downloads/),选择带有**devkit**的安装包下载。

​	[![R5sop6.png](https://z3.ax1x.com/2021/07/05/R5sop6.png)](https://imgtu.com/i/R5sop6)

[![R5sjAA.png](https://z3.ax1x.com/2021/07/05/R5sjAA.png)](https://imgtu.com/i/R5sjAA)

[![R5yucT.png](https://z3.ax1x.com/2021/07/05/R5yucT.png)](https://imgtu.com/i/R5yucT)

安装完毕。

## 1.3、安装jekyll v 4.2.0版本

​	打开cmd,运行以下指令。

​	`gem install jekyll -v 4.2.0`

​	安装依赖

​	`gem install jekyll-paginate`

​	`gem install sdbm`

​	`gem install webrick`

​	`gem install net-telnet`

​	`gem install xmlrpc`

## 1.4、运行网站首页并且调试

​	在电脑的空白处，使用**git**下载网页的仓库。

​	`git clone https://github.com/zhao-guang-nan/zhao-guang-nan.github.io`

​	然后通过cmd的界面 `cd zhao-guang-nan.github.io`，通过下面的命令可以完成jekyll本地服务器的搭建。

​	`jekyll s --host "127.0.0.1" --port 8080`打开服务器。

​	此时，cmd的命令行会有信息提示：

```bash
...
Server address: http://127.0.0.1:8080/
  Server running... press ctrl-c to stop.
  ...
```

证明本地环境搭建成功。

此时，可以打开浏览器，访问以下地址：

```http
http://127.0.0.1:8080/
```

就可以打开访问`zhao-guang-nan.github.io`的网页了，可以查看自己写的markdown是否已经加载成功了。

## 1.5、卸载与重装Jekyll

​	卸载**jekyll**的方法

​	`gem uninstall jekyll`

​	卸载完毕之后，然后执行

​	`gem install jekyll -v 4.2.0`

​	就可以重新安装了。

​	