---
title: hexo 文章中插入图片问题
date: 2017-05-13 17:57:49
categories: 系统配置
tags: hexo
---



1.首先确认`_config.yml` 中有 `post_asset_folder:true`。
Hexo 提供了一种更方便管理 Asset 的设定：`post_asset_folder`
当您设置`post_asset_folder`为`true`参数后，在建立文件时，`Hexo`
会自动建立一个与文章同名的文件夹，您可以把与该文章相关的所有资源都放到那个文件夹，如此一来，您便可以更方便的使用资源。

<!--more-->

2.在hexo的目录下执行`npm install https://github.com/CodeFalling/hexo-asset-image --save`（需要等待一段时间）。

3.完成安装后用`hexo`新建文章的时候会发现`_posts`目录下面会多出一个和文章名字一样的文件夹。图片就可以放在文件夹下面。结构如下：

```
本地图片测试
├── apppicker.jpg
├── logo.jpg
└── rules.jpg
本地图片测试.md
```

这样的目录结构（目录名和文章名一致），只要使用 `![logo](本地图片测试/logo.jpg)`就可以插入图片。其中`[]`里面不写文字则没有图片标题。
生成的结构为

```
public/2016/3/9/本地图片测试
├── apppicker.jpg
├── index.html
├── logo.jpg
└── rules.jpg
```

同时，生成的 html 是

`<img src="/2016/3/9/本地图片测试/logo.jpg" alt="logo">`

而不是愚蠢的

`<img src="本地图片测试/logo.jpg" alt="logo">`