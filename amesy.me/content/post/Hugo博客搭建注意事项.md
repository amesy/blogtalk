---
title: "Hugo博客搭建注意事项"
date: 2020-04-11T13:49:40+08:00
lastmod: 2020-04-11T13:49:40+08:00
hidden: false
draft: false
tags: ["博客搭建"]
categories: ["博客搭建"]
description: ""
slug: "Set up the blog"
---

<!--more-->

好久没折腾博客了。最早开始用的是 WordPress，购买云主机和域名，一步步搭建，乐此不疲。后来更新博客频率不高，慢慢的就搁置了。再后来迷上了静态博客框架，如 Hexo、Jekyll 等，借助 Github Page 搭建简单且免费。

了解到 Hugo 是因为接触 Golang 的缘故。Hugo 号称是“世界上最快的网站构建框架”，且由 go 开发，最近接触下来，感觉还不错，并且很喜欢目前的这款博客主题，也该是时候"换套新衣服"了。

搭建过程中，踩到了几个坑，这里总结记录一下，以防忘记。

首先搭建步骤参照 [Hugo中文文档](https://www.gohugo.org/) 即可搭建成功。Hugo 各个 Release [点这里](https://github.com/gohugoio/hugo/releases)下载。
我使用的主题是 [Even](https://github.com/olOwOlo/hugo-theme-even/blob/master/README-zh.md)， 其中 README 文档中特别提醒如下信息，否则提交的文章在浏览器上浏览时会显示空白。

>   对于这个主题，你应该使用 **post** 而不是 **posts**，即 `hugo new post/some-content.md`。

其次，所有静态页面都会生成到 public 目录，而这个目录一开始执行完 `hugo new site` 命令是没有的，只有执行完 `hugo` 或 `hugo server` 才会生成该目录。本地调试时也是一样，默认静态页面文件会生成到本地根目录，即 `hugo new site`命令指定的目录下。 所以需要通过 `-d` 选项手动指定存放静态文件的目标目录，也就是 public 目录，比如：

```shell
$ hugo --theme=even --baseUrl="https://amesy.me/" -d public
$ hugo server -d public
```

如果拥有自己的域名，就可以通过 CNAME 解析到 xxx.github.io 域名上。CNAME 域名需要放到 public 目录下。

评论系统以 **gittalk - a comment system based on GitHub issues** 为例。Even 主题默认集成了 gittalk ，所以不需要额外修改主题代码，直接在配置文件对应处进行配置即可。配置的参数有这四项：

```markdown
# Your GitHub ID
owner = ""	
# The repo to store comments		
repo = ""	
# https://github.com/settings/developers
# Your client ID		 
clientId = ""	
# Your client Secret
clientSecret = ""   
```	 
 
参考我的配置：

**github repo:**

![blogtalk](https://p.qlogo.cn/qqmail_head/C6nnRGnPbvwlVslNHxDtemvOjTjEDAZ1u8e7mDoVhQrVo6vIjsUxkKjEsFiabqjCHjusYcDBrckE/0)

**github application:**

![applications](https://p.qlogo.cn/qqmail_head/C6nnRGnPbvwlVslNHxDtemvOjTjEDAZ1u8e7mDoVhQrsaibOTtJ8hkB6ZfYriaibZ2HBfUrvicDG9ek/0)


部署时只需要提交 public 中的文件到 Repository，然后就可以通过域名愉快的访问了。</br>
最后，如果初次搭建，可能还会碰到形形色色的问题，只要善用搜索引擎，问题基本都能解决。





