---
title: Hexo + NexT 博客搭建
date: 2018-01-01
lastmod: 2022-04-20
categories:
- Tech
tags:
- Tool
---

基于 Hexo 和 NexT 生成 blog

<!--more-->

# Hexo

Hexo 是一个博客框架，使用 Markdown 解析文章生成静态网页。

通过 [Hexo 官方网址](https://hexo.io/zh-cn/docs/)进行安装与配置，安装完 Node.js 后使用命令行进行安装。

# NexT

NexT 是一个主题，可以运行在 Hexo 所搭建的 blog 上。

通过 [NexT 官方网址](https://theme-next.js.org/) 进行与 Hexo 的配置。

<mark>推荐使用 `node_modules` 安装，而非下载至 `theme` 文件夹中</mark>

# 写作

## 使用图床

选择 [Imgur](https://imgur.com/) 进行图片的托管，内置支持六种不同尺寸的图片，例如原图片的地址为 `https://i.imgur.com/test.jpg`，则六种尺寸的对应地址为：

- Huge Thumbnail 1024*1024: `https://i.imgur.com/testh.jpg`
- Large Thumbnail 640*640: `https://i.imgur.com/testl.jpg`
- Medium Thumbnail 320*320: `https://i.imgur.com/testm.jpg`
- Small Thumbnail 160*160: `https://i.imgur.com/testt.jpg`
- Big Square 160*160: `https://i.imgur.com/testb.jpg`
- Small Square 90*90: `https://i.imgur.com/tests.jpg`

后两个选项将会对图片进行裁剪而非保持比例

## 使用 NoteTag、Tabs、Label 标签

见 [Tag Plugins](https://theme-next.js.org/docs/tag-plugins/)

各种 Tag 的配置在 `_config.next.yml` 中

# 功能添加

## 字数统计

安装 [symbols_count_time](https://github.com/theme-next/hexo-symbols-count-time) 修改 `_config.yml` 和 `_config.next.yml` 文件

## 站内搜索

安装 Hexo 插件 [hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)，修改 `_config.yml` 和 `_config.next.yml` 文件

## Emoji 支持

安装 [hexo-filter-github-emojis](https://github.com/crimx/hexo-filter-github-emojis) 插件，修改 `_config.yml` 文件

使用语法 `:smile:`

## RSS 支持

安装 Hexo 插件 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)，修改 `_config.yml` 文件，并在 Next 设置中开启 RSS 图标

## 外链优化

安装 [hexo-filter-nofollow](https://github.com/hexojs/hexo-filter-nofollow)，修改 `_config.yml` 文件

## 死链检查

安装 [hexo-broken-links-checker](https://github.com/sergeyzwezdin/hexo-broken-links-checker)，修改 `_config.yml` 文件

## 网页压缩

提升网页响应速度

安装 [hexo-neat](https://github.com/rozbo/hexo-neat) 插件，修改 `_config.yml` 文件

## 分享按钮

使用 [AddThis Share](https://www.addthis.com)，修改 `_config.next.yml` 文件

## 评论功能

使用 [Utterances](https://utteranc.es)，修改 `_config.next.yml` 文件

## 打赏

在 `next/source/images` 添加 `alipay.png` 和 `wechatpay.png`，修改 `_config.next.yml` 文件：

```yaml
reward_settings:
  enable: true
  animation: false
  comment: [comment]

reward:
  wechatpay: /images/wechatpay.png
  alipay: /images/alipay.png
```

也可以添加自定义打赏：

```yaml
reward:
  Venmo: /images/venmo.jpg
```

## 加入搜索引擎和 sitemap

设置 [Google 站点管理工具](https://www.google.com/webmasters/tools/)的验证字符串，添加自己网址并导航到验证方法，选择 `HTML 标签`，获取代码

```html
<meta name="google-site-verification" content="XXXXXXXXXXXXXXXXXXXXXXX" />
```

将 `content` 中的内容复制，修改 `_config.next.yml` 文件

提交站点地图：安装 [hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap)，修改 `_config.yml` 文件，之后生成发布会新出现 `sitemap.xml` 文件，将其提交至 Google。

# 备份与自动部署

1. 安装 [hexo-deploy-git](https://github.com/hexojs/hexo-deployer-git) 插件，使用 http 和 token 进行部署

2. GitHub 新建 private repo 用来保存设置文件（设置文件中包含个人私密信息），在 hexo 主目录下 `git init`，并在 `.gitignore` 文件中添加不需要跟踪的文件

   ```text
   db.json
   *.log
   package-lock.json
   node_modules/
   public/
   .deploy*/
   ```

3. 在 Github 申请 `Settings -> Developer settings -> Personal access tokens`

4. 在仓库中 `Setting -> Secrets` 添加申请的 token，命名为 `[HEXO_PUBLISH]`

5. 新建 Github Action，内容如下

   ```yaml
   name: Hexo Deploy

   on:
     push:
       branches: [ master ]

   jobs:
     build:
       runs-on: ubuntu-latest

       steps:
         - uses: actions/checkout@v2
         - uses: actions/setup-node@v2.1.5
           with:
             node-version: 15.12.0

         - name: env setup
           run: |
             git config --global user.email "<Email>"
             git config --global user.name "<Username>"
             sudo apt install pandoc
             npm i -g hexo-cli
             npm install
         - name: change post.njk
           run: |
             cp source/_data/post.njk_custom node_modules/hexo-theme-next/layout/_macro/post.njk
         - name: hexo deploy
           run: |
             export GITHUB_TOKEN="${{secrets.HEXO_PUBLISH}}"
             hexo g -d
   ```

6. 之后只需要 push 到 GiHub 仓库上即会自动部署。<mark>本地记得定期更新插件以及对应配置</mark>

# CSS 样式更改

在 `_config.next.yml` 中启用 `custom_file_path/style: source/_data/styles.styl`

## 修改标题格式

```stylus
.post-body {
  h1 {
    font-size: 1.7em;
    text-align: center;
  }

  h2 {
    font-size: 1.5em;
    text-decoration: underline;
  }

  h3 {
    font-size: 1.3em;
  }
}
```

## 更改列表嵌套序号样式

```stylus
.posts-expand .post-body {
  ul li { list-style: disc; }
  ul li li { list-style: circle; }
  ul li li li {list-style: square;}

  ol li { list-style: decimal;}
  ol li li {list-style: lower-roman;}
}
```

## 更改列表间距

```stylus
.post-body {
  p+ul {
    margin-top: -1em;
  }

  p+ol {
    margin-top: -1em;
  }
}
```

## 更改图片与标题间距

```stylus
.post-body {
  .image-caption, img + figcaption, .fancybox + figcaption {
    margin: 0px auto 15px;
  }
}
```

## 修改引用格式

```stylus
.post-body {
  blockquote {
    border-left: 4px solid #92c1f0;
    background-color: rgb(245, 245, 245);
  }
}
```

# 问题

## 解决 MathJax 渲染问题

更换渲染器为 [Pandoc](https://pandoc.org/installing.html)，首先在官网安装最新版，然后更换 Hexo 渲染器：

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

## MathJax3

在数学渲染引擎更改为 MathJax3 之后，由于 MathJax3 暂不支持 `\\` 自动换行（参考 [issue](https://github.com/mathjax/MathJax/issues/2312)），因此很多文章出现问题。

想了一下其实是没有遵守正规的写法，参考 [AMS 规范](https://silencial.github.io/writing-rule/#ams-规范)

## 仅在摘要显示图片

修改 `_config.next.yml`：

```yaml
excerpt_description: false
```

修改 `/[next_path]/layout/_macro/post.njk`：

```nginx title https://google.com link text
/*在{% if post.description and theme.excerpt_description %} 与 {% elif post.excerpt  %}之间添加*/
{% elif post.image  %}
  <div class="out-img-topic">
    <img src={{ post.image }} class="img-topic" />
  </div>
  {% if post.excerpt %}
    {{ post.excerpt }}
    <!--noindex-->
    <div class="post-button text-center">
      <a class="btn" href="{{ url_for(post.path) }}{% if theme.scroll_to_more %}#more{% endif %}" rel="contents">
        {{ __('post.read_more') }} &raquo;
      </a>
    </div>
    <!--/noindex-->
  {% else %}
    <!--noindex-->
    <div class="post-button text-center">
      <a class="btn" href="{{ url_for(post.path) }}">
        {{ __('post.read_more') }} &raquo;
      </a>
    </div>
    <!--/noindex-->
  {% endif %}
```

如果要显示图片则在 `.md` 文件的 `Front-matter` 中增加 `image: [image_path]`，此时不需要写 `<!-- more -->`

若使用 `node_modules` 安装 `NexT`，且使用 GitHub 自动部署，则应该将修改后的 `post.njk` 文件复制到 `source/_data/post.njk_custom` （以 `.njk` 结尾的文件好像会被自动加载，因此改名），在自动部署时复制回原始位置覆盖。

## 图片标题问题

在 `_config.next.yml` 中启用 `fancybox`。插入图片时使用标题

```markdown
![alt](123.png)
```

fancybox 会解析 `alt` 标签作为图片标题，并且有良好的格式，放大后也会在下方显示。

关于图片和标题重叠问题，参见 [更改图片与标题间距](#更改图片与标题间距)

## iframe 与 pdf tag 不加载

应该使用 `https` 的网址

## 双大括号

双大括号 `{{}}` 是 nunjucks 语法，要避免在文章中出现 <mark>一般是数学环境</mark>

# Future

1. 增加 about 页面

## 其它插件

- [思维导图 tag](https://github.com/MaxChang3/hexo-markmap)
- [时间线格式 tag](https://github.com/wangwei1237/hexo-tag-mdline)
- [使用 Chartjs 画图](https://github.com/Shen-Yu/hexo-tag-chart)
