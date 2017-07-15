---
title: hexo-theme-indigo-card主题安装及常见问题解决
date: 2017-05-13 14:37:43
categories: 系统配置
tags: hexo
---

# 安装

安装需确认你的 Hexo 版本在 3.0 以上，以及 Node 版本为 6.x 以上，在 Hexo 根目录，执行以下命令。

```
git clone git@github.com:yscoder/hexo-theme-indigo.git themes/indigo

```

<!--more-->

## 切换主题

执行 `git branch` 显示所有本地分支，如果只存在一个分支，可以执行下面的命令获取另一分支的主题。

```
# 获取远程 card 分支，并切换
$ git checkout -b card origin/card

# 获取远程 master 分支，并切换
$ git checkout -b master origin/master
```

此命令只需执行一次，之后使用 `git checkout [branch]` 命令在两个主题之间切换。

## 依赖安装

还是在 Hexo 根目录，如果以下插件已安装过，无需再次安装。

### Less

主题默认使用 less 作为 css 预处理工具。

```
$ npm install hexo-renderer-less --save

```

### Feed

用于生成 rss。

```
$ npm install hexo-generator-feed --save

```

### Json-content

用于生成静态站点数据，用作站内搜索的数据源。

```
$ npm install hexo-generator-json-content --save

```

### QRCode

用于生成微信分享二维码。

> 可选，不安装时会请求 jiathis Api 生成二维码。

```
$ npm install hexo-helper-qrcode --save

```

## 开启标签页

```
hexo new page tags

```

修改 `hexo/source/tags/index.md` 的元数据

```
layout: tags
comments: false
---
```

## 开启分类页

仅 card theme 支持。

```
hexo new page categories

```

修改 `hexo/source/categories/index.md` 的元数据

```
layout: categories
comments: false
---
```

# 常见问题

## 如何设置文章摘要

在 Markdown 中加 `<!-- more -->`

## 文章如何添加多个标签

有两种多标签格式

```
tags: [a, b, c]
```

或

```
tags: 
  - a
  - b
  - c
```

## 修改 brand 图片（菜单上方背景图）

替换 `themes\indigo\source\img\brand.jpg`，保持原文件名不变。

### 如何在文章中使用图标

先到 [fontawesome](http://fontawesome.io/icons/) 找到你需要的图标名，比如：`book`，按以下格式使用：

```
<i class="icon icon-book"></i>
```

图标样式前缀均为 `icon`，此外还有 5 个图标大小调节类和 1 个间距类。

```
<!-- 1.3倍大小 -->
<i class="icon icon-book icon-lg"></i>
<!-- 2倍大小 -->
<i class="icon icon-book icon-2x"></i>
<!-- 3倍大小 -->
<i class="icon icon-book icon-3x"></i>
<!-- 4倍大小 -->
<i class="icon icon-book icon-4x"></i>
<!-- 5倍大小 -->
<i class="icon icon-book icon-5x"></i>
<!-- 5px右边距 -->
<i class="icon icon-book icon-pr"></i>
<!-- 5px左边距 -->
<i class="icon icon-book icon-pl"></i>
```

## 个别图标无法显示

如果你的浏览器安装了 ADBlock，它会屏蔽 SNS 相关的内容，比如：Github。

解决办法：可配置 ADBlock 不在你的站点运行。

## 生成站点后没有样式

[安装less](https://github.com/yscoder/hexo-theme-indigo/wiki/%E5%AE%89%E8%A3%85)

## 更改样式后网站没有生效

确认非缓存问题后，执行 `hexo clean` 再进行生成上传。

## 更改站点配色

编辑 `themes\indigo\source\css\_partial\variable.less`，更改对应的颜色变量。

配色参考：[Material Design Color Palette Generator](http://www.materialpalette.com/)

## 添加404页面

在 `hexo/source` 目录内新建 `404.html`。

设置元数据信息，如果不想套用主题布局可设置 `layout` 为 `false`。

```
layout: false    
title: "My Blog Name | 404"
---
```

## 在博客中使用 Emoji

参考 [Can i use emoji in mypage?](https://github.com/yscoder/hexo-theme-indigo/issues/90)

## 多说

- 多说配置，取你的多说后台网址二级域名。比如我的是：`http://ysblog.duoshuo.com/admin/`中的 `ysblog`。
- 评论中如果显示 HTML 标签，你需要 **进入多说设置 -> 评论解析 -> 解析HTML代码** 勾选上。
- 已本地化多说脚本和样式，有个人需求的可以自行修改相关样式 `source/css/_duoshuo/*` 和脚本 `source/css/js/embed.js`。

# 配置

# 站点配置

编辑站点配置文件，`hexo/_config.yml`。

## 启用主题

```
theme: indigo
```

## 基本配置

为了得到更好的使用体验，以下内容请务必填写完整，因为这些内容会在主题中得到展示。[更多](https://hexo.io/zh-cn/docs/configuration.html)

```
title: your title
subtitle: your subtitle
description: your description
keywords: your keywords
author: your name
email: your email
url: your site url
```

## feed配置

参考 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)

```
feed:
  type: atom
  path: atom.xml
  limit: 0
```

## jsonContent配置

为了节约资源，可以对 jsonContent 插件生成的数据字段进行配置，减少数据文件大小。参考 [hexo-generator-json-content](https://github.com/alexbruno/hexo-generator-json-content)

```
jsonContent:
  meta: false
  pages: false
  posts:
    title: true
    date: true
    path: true
    text: true
    raw: false
    content: false
    slug: false
    updated: false
    comments: false
    link: false
    permalink: false
    excerpt: false
    categories: false
    tags: true
```

# 主题配置

编辑主题配置文件，`themes/indigo/_config.yml`。

## 左侧菜单

默认配置如下

```
menu:
  home:
    text: 主页
    url: /
  archives:
    url: /archives
  tags:
    url: /tags
  github:
    url: https://github.com/yscoder
    target: _blank
  weibo:
    url: http://www.weibo.com/ysweb
    target: _blank
  link:
    text: 测试
    url: /
```

添加新菜单项时，在 menu 下增加子属性即可。属性说明如下：

```
menu:
 link:               # fontawesome图标，省略前缀，本主题前缀为 icon-，必须
   text: About       # 菜单显示的文字，如果省略即默认与图标一致，首字母会转大写
   url: /about       # 链接，绝对或相对路径，必须
   target: _blank    # 是否跳出，省略则在当前页面打开
```

fontawesome 图标已集成到主题中，你可以到 [这个页面](http://fontawesome.io/icons/) 挑选合适的图标。

## rss

```
rss: /atom.xml
```

## favicon

站点 logo，显示在浏览器当前标签页左上角。

```
favicon: /favicon.ico
```

## 头像

位于左侧菜单上方

```
avatar: /img/logo.jpg
```

## email

头像下方

```
email: 634206017@qq.com
```

## color

设置 Android L Chrome 浏览器状态栏颜色，不需要可去除此项或设为 `false`。

```
color: '#3F51B5'
```

## 标签页 (old)

配置标签页标题

```
tags:
  title: 标签
```

## 页面标题 (card theme)

自定义归档、标签、分类页的大标题。

```
tags_title: Tags
archives_title: Archives
categories_title: Categories
```

## 文章摘要

可以在 Markdown 文件中加 `<!--more-->`以分割摘要与文章正文。未设置时，按 `excerpt_length`设置截取。

```
# 文章摘要渲染方式: 为 true 时将渲染为 html，否则为文本
excerpt_render: false
# 截断长度
excerpt_length: 200
# 文字正文页链接文字
excerpt_link: 阅读全文...
```

## mathjax

开启后，使你的站点支持公式渲染，by [mathjax](https://www.mathjax.org/)。 请按需开启，因为此项需要加载额外的 js 文件。

```
mathjax: false
```

## 分享

文章分享开关，by [jiathis-api](http://www.jiathis.com/help/html/share-with-jiathis-api)。

```
share: true
```

## 文章打赏

默认开启

```
reward:
  title: 谢谢大爷~             #显示的文字
  wechat: /img/wechat.jpg     #微信，关闭设为 false
  alipay: /img/alipay.jpg     #支付宝，关闭设为 false
```

此外在 crad theme 中，可以通过在 markdown 头部添加 `reward: false` 来控制某些不想开启打赏的页面。

关闭

```
reward: false
```

> 二维码请自行从微信、支付宝中下载。当两个二维码同时存在时，为保持显示效果的一致性，注意截图时的边框留白保持一致。必要时可借助PS等图片处理工具进行图片大小裁剪、压缩等。

## 站内搜索

是否开启搜索

```
search: true
```

## 布局

开启后，文章页在大屏下会隐藏左侧菜单，专注阅读。

```
hideMenu: true
```

## Toc

开启文章内容导航。

```
#toc: false  #关闭
toc:
  list_number: false  # 决定导航使用的标签， true 为 ol， false 为 ul。
```

## copyright (card theme)

文章页版权声明内容，hexo中所有变量及辅助函数等均可调用，具体请查阅 [hexo.io](http://hexo.io/)。

```
copyright: 这里写留言或版权声明：<a href="<%- url_for(page.path) %>" target="_blank" rel="external"><%- url %></a>
```

## less

设置 less 编译时的入口文件路径，[hexo-renderer-less](https://github.com/hexojs/hexo-renderer-less)。

```
less:
  compress: true    # 是否压缩css
  paths:
    - source/css/style.less
```

## 评论

集成了[多说](http://duoshuo.com/)和 [disqus](https://disqus.com/)，开启其一即可。

> duoshuo-key 即多说创建站点时的二级域名。如：`abc.duoshuo.com`，就填 `abc`。

```
duoshuo: duoshuo-key
```

或

```
disqus_shortname: disqus_shortname
```

## 数据统计

集成的有谷歌和 CNZZ，请填写你的站点标识。

```
google_analytics: key
cnzz: 站点id
```

## 谷歌站点验证 (card theme)

```
google_site_verification: false
```

## 规范网址 (card theme)

让搜索引擎重定向你的不同域名、不同子域、同域不同目录的站点到你期望的路径。[使用规范网址](https://support.google.com/webmasters/answer/139066)

```
canonical: http://imys.net
```

## 版权起始年份

```
since_year: 2006
```

## 自定义页面关于

用户页面中作者相关的描述性文字，如不需要设为 false

```
about: 用户页面中作者相关的描述性文字，如不需要设为 false
```