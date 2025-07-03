---
title: Hugo 博客搭建
date: 2024-03-19
lastmod: 2025-07-01
categories:
- Tech
tags:
- Tool
---

最近重燃了写博客的热情，计划在内容上增添更多个人主题，而不仅限于纯粹的技术分享。

由此开始了新一轮的博客引擎迁移。此前的博客是使用 Hexo 搭配 NexT 主题搭建，并通过 GitHub Actions 实现推送后自动构建与部署，具体可参见 [Hexo + NexT 博客搭建](/posts/hexo-blog-setup)。然而，Hexo 的依赖管理日渐繁琐，使我不想再对已配置好的模板进行大改。此时恰巧发现了 Hugo 这个更为简洁、高效的静态博客引擎，便决定将博客迁移至该平台。

<!--more-->

---

# 安装

首先，通过 Homebrew 安装 Hugo：

```sh
brew install hugo
```

接着，创建一个新的博客站点：

```sh
hugo new site <site_name> --format yaml
```

该命令会在当前目录下创建一个名为 `site_name` 的新文件夹。Hugo 默认使用 `toml` 作为配置文件语法，但我个人更倾向于 `yaml` 格式，因此在创建时通过 `--format yaml` 参数进行了指定。

文章的 Markdown 文件应存放于 `<root>/content/posts/` 目录下。完成基本配置后，运行 `hugo server` 命令，即可在本地预览渲染后的静态博客。更详细的目录结构说明，可以参考[官方文档](https://gohugo.io/getting-started/directory-structure/)。

# 主题配置

我选择了设计简洁的 [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 主题，并在此基础上进行了一系列个性化定制。

1. Fork 主题仓库：首先，将主题仓库 Fork 到自己的 GitHub 账户下。这样做既方便进行个性化修改，也便于后续与上游仓库同步更新。
2. 添加主题为子模块：在博客项目的根目录下，使用 Git Submodule 的方式将 Fork 后的主题仓库添加至 `themes/` 文件夹：

    ```sh
    git submodule add --depth=1 git@github.com:<username>/hugo-PaperMod.git themes/PaperMod
    ```

3. 启用主题：在 `hugo.yaml` 配置文件中，添加 `theme: 'PaperMod'` 以启用该主题。

以下所有修改的最终代码，均可在我的 [PaperMod Fork](https://github.com/silencial/hugo-PaperMod) 仓库中找到。

## 添加 MathJax 支持

为使博客支持 LaTeX 数学公式渲染，需进行如下配置，具体可参照[官方文档](https://gohugo.io/content-management/mathematics/)。

1. 更新 Hugo 配置：在 `hugo.yaml` 中添加相关设置，使 Hugo 内置的 `Goldmark` Markdown 渲染器能够正确解析 `$` 和 `$$` 数学环境。
2. 创建 MathJax 模板：在主题文件夹下新建 `layouts/partials/math.html` 文件，用于引入 MathJax 脚本并进行配置。完整的配置选项可参阅 [MathJax 官方文档](https://docs.mathjax.org/en/v3.0-latest/options/input/tex.html)。我的配置中额外加入了 `tags: 'ams'` 以支持公式的自动编号和引用，并引入了 `mathtools` 包以使用更多常见命令。

    ```html
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js"></script>
    <script>
        MathJax = {
            tex: {
                displayMath: [['$$', '$$']],  // block
                inlineMath: [['$', '$']],     // inline
                tags: 'ams',
                packages: {
                    "[+]": ["mathtools"]
                }
            },
            "loader": {
                load: ["[tex]/mathtools"]
            }
        };
    </script>
    ```

3. 引入 MathJax 模板：修改主题的 `layouts/_default/baseof.html` 文件，在 `<head>` 部分加入判断逻辑，仅在文章参数中启用 `math` 时加载该模板。

    ```html
    <head>
        ...
        {{ if .Param "math" }}
            {{ partialCached "math.html" . }}
        {{ end }}
        ...
    </head>
    ```

**问题与解决**：

- 公式过长导致页面溢出。解决方法是在 `assets/css/extended/blank.css` 中为 MathJax 容器添加横向滚动条：

    ```css
    mjx-container {
        overflow-x: auto;
        overflow-y: hidden;
        max-width: 100%;
    }
    ```

- 带编号的公式仍然存在溢出问题。解决办法是添加额外的样式规则以约束其最小宽度：

    ```css
    .MathJax {
        min-width: auto !important;
    }
    ```

- 标题中包含公式时渲染错乱。解决方法是调整标题提示元素的显示属性：

    ```css
    .entry-hint-parent {
        display: unset;
    }
    ```

## 图注

为了给图片添加图注（Caption），我的实现方式是利用 Markdown 图片语法中的 `alt` 文本。

由于 Markdown 语言中图片的语法 `![alt text](image.png "title text")` 在转化为 HTML 时会变为 `<img src="image.png" alt="alt text" title="title text">`。因此，修改 `layouts/_default/_markup/render-image.html` 文件，将 `alt` 文本渲染为 `<figcaption>`：

```html
<figure>
<img ... >
{{ if .Text}}
<figcaption>{{ .Text | safeHTML }}</figcaption>
{{ end }}
</figure>
```

并且在 `assets/css/extended/blank.css` 文件中添加如下样式，美化图注的格式：

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

为实现 `Categories` 菜单项下的二级下拉菜单，需进行如下配置：

1. 修改 `layouts/partials/header.html` 文件，加入处理二级菜单的逻辑。
2. 在 `hugo.yaml` 的 `menu` 部分定义二级菜单的层级结构。

## 标签云

为生成标签云页面，需进行以下操作：

1. 修改 `layouts/_default/terms.html` 模板文件。
2. 在 `assets/css/extended/blank.css` 中添加对应的 CSS 样式。

## Social Icon

若要添加或修改社交链接图标，需编辑 `themes/PaperMod/layouts/partials/svg.html` 文件。可以从 [Simple Icons](https://simpleicons.org/) 网站查找所需图标，并将其 SVG 代码复制到文件中。

## 显示文章更新时间

为了在文章元信息中同时显示最近更新时间，需编辑 `layouts/partials/post_meta.html` 文件并加入以下逻辑：

```html
{{- if not .Lastmod.IsZero -}}
{{- $scratch.Add "meta" (slice (printf "Updated:&nbsp;%s" (.Lastmod | time.Format ":date_medium"))) }}
{{- end }}
```

## Mermaid 图支持

参照[官方文档](https://gohugo.io/content-management/diagrams/)，可以为 Hugo 添加 Mermaid.js 支持，从而在 Markdown 中直接渲染流程图、时序图等。

## Callout 支持

通过集成 [hugo-admonitions](https://github.com/KKKZOZ/hugo-admonitions) 模块，可以方便地在文章中使用 Callout/Admonition 块（如 `note`, `warning`, `tip` 等）。

# GitHub Pages 发布

部署过程可直接参考官方文档：[Hosting on GitHub Pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/)。

# TODO

- 将文章目录（TOC）移至侧边栏，并实现滚动时高亮当前章节的功能。目前尚未找到理想的解决方案。
