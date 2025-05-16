---
title: Hugo 博客搭建
date: 2024-03-19
lastmod: 2025-03-19
categories:
- Tech
tags:
- Tool
---

最近又突然想写 Blog 了，而且希望加入一些个人主题，不再是以前的纯技术型？

于是又开始了新一轮的折腾。原来是用 Hexo + NexT 在 GitHub Pages 上搭建的 Blog，并通过 Github Actions 实现本地修改 push 到仓库后自动 build。详情参照 [Hexo + NexT 博客搭建]({{< ref "hexo-blog-setup" >}})。但 Hexo 的包管理实在太麻烦，多年以前搞好了一个模板之后实在不想再在上面改动了，恰巧发现 Hugo 这个更简单清爽静态博客引擎，于是打算换过来。

<!--more-->

---

# 安装

安装 Hugo：

```sh
brew install hugo
```

新建 Blog：

```sh
hugo new site <site_name> --format yaml
```

即会在当前目录下创建一个名为 `site_name` 的文件夹。Hugo 默认的配置语法为 `toml`，这里换成了 `yaml`，配置起来更舒服一些。

目录结构可以参照[官方文档](https://gohugo.io/getting-started/directory-structure/)。简单来说，文章（Markdown 文件）应该存放在 `<root>/content/posts/` 文件夹下。然后通过 `hugo server` 命令即可在本地渲染出静态博客。

# 主题配置

选取的主题为比较简单的 [PaperMod](https://github.com/adityatelange/hugo-PaperMod)，并在上面做了一些修改。

1. Fork 主题到自己的 GitHub 仓库，方便自己修改并与原仓库的更新合并。
2. 在博客文件夹下，使用 Git Submodule 方式添加主题至 `themes/` 文件夹下：`git submodule add --depth=1 git@github.com:<username>/hugo-PaperMod.git themes/PaperMod`。这里的 `username` 需要换成自己 GitHub 的，从而能够更改和推送。
3. `hugo.yaml` 配置文件中添加 `theme: 'PaperMod'`。

以下所有改动均可在 Fork 后的 [PaperMod](https://github.com/silencial/hugo-PaperMod) 中找到。

## 添加 MathJax 支持

参照[官方文档](https://gohugo.io/content-management/mathematics/)。

1. `hugo.yaml` 中添加配置，使得 Hugo 默认的 `Goldmark` Markdown 渲染器能够识别数学环境 `$` 和  `$$`。

2. 主题文件夹下新建 `layouts/partials/math.html`，引用 MathJax JS 来处理数学环境，这里可以设置一些参数，完整的设置参见 [MathJax 官方文档](https://docs.mathjax.org/en/v3.0-latest/options/input/tex.html)。我这里增加了一条 `tags: 'ams'`，从而可以实现数学公式的编号和引用。

   ```css
   <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js"></script>
   <script>
     MathJax = {
       tex: {
         displayMath: [['$$', '$$']],  // block
         inlineMath: [['$', '$']],     // inline
         tags: 'ams',
       }
     };
   </script>
   ```

3. 主题文件夹下修改 `layouts/_default/baseof.html`，引入 `math.html` 处理。

   ```css
   <head>
       ...
       {{ if .Param "math" }}
           {{ partialCached "math.html" . }}
       {{ end }}
       ...
   </head>
   ```

**问题**：

- 数学公式过长时会超出页面宽度，为此需要添加滚动样式：在 `assets/css/extended/blank.css` 添加对应代码

  ```css
  mjx-container {
      overflow-x: auto;
      overflow-y: hidden;
      max-width: 100%;
  }
  ```

- 有编号时 MathJax 依然会超出页面宽度，需要再添加一个样式

  ```css
  .MathJax {
      min-width: auto ! important
  }
  ```
  
- 标题中存在公式时格式存在问题，修改为

  ```css
  .entry-hint-parent {
      display: unset;
  }
  ```

## 图片 Caption

Markdown 语言中图片的语法

```markdown
![alt text](image.png "title text")
```

在转化为 html 时会变为

```html
<img src="image.png" alt="alt text" title="title text">
```

目前我的做法是将 `alt` 作为图片的标题使用。修改 `layouts/_default/_markup/render-image.html` 为

```html
<figure>
<img ... >
{{ if .Text}}
<figcaption>{{ .Text | safeHTML }}</figcaption>
{{ end }}
</figure>
```

在 `assets/css/extended/blank.css` 中添加简单样式

```css
.post-content figure > figcaption {
    color: #999;
    font-size: 14px;
    font-weight: 700;
    line-height: 1;
    margin: -10px auto 15px;
    text-align: center;
}
```

## 二级菜单

希望在 `Categories` 菜单栏下显示二级菜单：

1. 修改 `layouts/partials/header.html` 文件，加入二级菜单处理代码。
2. 在 `hugo.yaml` 中加入菜单目录。

## 标签云

1. 修改 `layouts/_default/terms.html`。
2. 在 `assets/css/extended/blank.css` 中添加相应 CSS 格式。

## Social Icon

修改 `themes/PaperMod/layouts/partials/svg.html`。可以在 [Simple Icons](https://simpleicons.org/?q=notion) 中找到想要的 Icon 并复制对应的代码至文件中。

## 显示文章更新时间

在 `layouts/partials/post_meta.html` 中添加

```html
{{- if not .Lastmod.IsZero -}}
{{- $scratch.Add "meta" (slice (printf "Updated:&nbsp;%s" (.Lastmod | time.Format ":date_medium"))) }}
{{- end }}
```

## Mermaid 图支持

参照[官方文档](https://gohugo.io/content-management/diagrams/)进行修改

## Callout 支持

使用 [hugo-admonitions](https://github.com/KKKZOZ/hugo-admonitions) 模块

# GitHub Pages 发布

参见[官方文档](https://gohugo.io/hosting-and-deployment/hosting-on-github/)。

# TODO

- 将 TOC 移动至侧边栏并随页面滚动定位至相应位置。没有找到好的解决方法。



