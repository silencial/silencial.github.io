---
title: Hexo + NexT 博客搭建
date: 2018-01-01
lastmod: 2025-06-24
categories:
- Tech
tags:
- Tool
---

基于 Hexo 和 NexT 主题搭建博客

<!--more-->

---

# Hexo

Hexo 是一个博客框架，它使用 Markdown 解析文章并生成静态网页。

通过 [Hexo 官方网站](https://hexo.io/zh-cn/docs/)进行安装与配置。安装完 Node.js 后，使用命令行完成 Hexo 的安装。

# NexT

NexT 是一个主题，可以运行在 Hexo 搭建的博客上。

通过 [NexT 官方网站](https://theme-next.js.org/) 进行与 Hexo 的配置。

> [!tip]
> 推荐使用 `node_modules` 方式安装，而非下载至 `theme` 文件夹中。

# 写作

## 使用图床

选择 [Imgur](https://imgur.com/) 进行图片托管。Imgur 内置支持六种不同尺寸的图片。例如，若原图地址为 `https://i.imgur.com/test.jpg`，则六种尺寸的对应地址为：

- Huge Thumbnail 1024x1024: `https://i.imgur.com/testh.jpg`
- Large Thumbnail 640x640: `https://i.imgur.com/testl.jpg`
- Medium Thumbnail 320x320: `https://i.imgur.com/testm.jpg`
- Small Thumbnail 160x160: `https://i.imgur.com/testt.jpg`
- Big Square 160x160: `https://i.imgur.com/testb.jpg`
- Small Square 90x90: `https://i.imgur.com/tests.jpg`

⚠️ 注意：后两个方形选项会对图片进行裁剪以符合尺寸，而非保持原始比例。

## 使用 Note、Tabs、Label 等标签

具体用法参见 [NexT 主题的标签插件文档](https://theme-next.js.org/docs/tag-plugins/)。

各种标签的全局配置位于 `_config.next.yml` 文件中。

# 功能添加

## 文章字数统计与阅读时长

安装插件 [hexo-symbols-count-time](https://github.com/theme-next/hexo-symbols-count-time)，并根据其文档修改 `_config.yml` 和 `_config.next.yml` 文件。

## 站内搜索

安装插件 [hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)，并根据其文档修改 `_config.yml` 和 `_config.next.yml` 文件。

## Emoji 支持

安装插件 [hexo-filter-github-emojis](https://github.com/crimx/hexo-filter-github-emojis)，并根据其文档修改 `_config.yml` 文件。

使用语法为 `:smile:`

## RSS 订阅支持

安装插件 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)，修改 `_config.yml` 文件，并在 NexT 主题设置中开启 RSS 图标。

## 外链优化

为所有外部链接自动添加 `rel="nofollow"` 属性，有助于 SEO。

安装插件 [hexo-filter-nofollow](https://github.com/hexojs/hexo-filter-nofollow)，并修改 `_config.yml` 文件。

## 死链检查

安装插件 [hexo-broken-links-checker](https://github.com/sergeyzwezdin/hexo-broken-links-checker)，并修改 `_config.yml` 文件。

## 网页压缩

压缩 HTML、CSS、JS 和图片文件，以提升网页加载速度。

安装插件 [hexo-neat](https://github.com/rozbo/hexo-neat)，并修改 `_config.yml` 文件。

## 评论功能

集成 [Utterances](https://utteranc.es) 评论系统（基于 GitHub Issues）。在 `_config.next.yml` 文件中进行配置。

## 打赏

在 `next/source/images` 目录下添加 `alipay.png` 和 `wechatpay.png` 二维码图片，并修改 `_config.next.yml` 文件：

```yaml
reward_settings:
    enable: true
    animation: false
    comment: 请我喝杯咖啡吧！ # 此处为提示文字

reward:
    wechatpay: /images/wechatpay.png
    alipay: /images/alipay.png
```

也可以添加自定义打赏方式，例如：

```yaml
reward:
    Venmo: /images/venmo.jpg
```

## 集成搜索引擎与 Sitemap

1. 登录 [Google Search Console](https://www.google.com/webmasters/tools/)，添加博客网站。
2. 在验证方法中选择 `HTML 标签`，复制验证代码中的 `content` 字符串，如下所示：

    ```html
    <meta name="google-site-verification" content="XXXXXXXXXXXXXXXXXXXXXXX" />
    ```

3. 将复制的 `content` 字符串粘贴到 `_config.next.yml` 文件的 `google_site_verification` 字段。
4. 安装 Sitemap 生成插件 [hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap) 并修改 `_config.yml`。
5. 重新生成并部署网站，新的 `sitemap.xml` 文件将会生成。
6. 将 `sitemap.xml` 的路径提交至 Google Search Console。

# 备份与自动部署

1. 安装 Git 部署插件：安装 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)。使用 HTTPS 和 Personal Access Token 进行部署。
2. 创建备份仓库：在 GitHub 上新建一个**私有**仓库，用于存放博客的源文件和配置文件（配置文件中包含私密信息）。
3. 初始化本地仓库：在 Hexo 主目录下执行 `git init`，并创建 `.gitignore` 文件以排除无需版本控制的文件：

    ```text
    db.json
    *.log
    package-lock.json
    node_modules/
    public/
    .deploy*/
    ```

4. 生成 Personal Access Token：在 GitHub `Settings -> Developer settings -> Personal access tokens` 页面生成一个新的 Token。
5. 配置仓库 Secret：在用于**部署**的公开仓库中，导航至 `Settings -> Secrets and variables -> Actions`，添加上一步生成的 Token，并命名为 `HEXO_PUBLISH`。
6. 创建 GitHub Action：在用于**备份**的私有仓库中，创建 `.github/workflows/deploy.yml` 文件，内容如下：

    ```yaml
    name: Hexo Deploy
    
    on:
        push:
            branches: master  # 主分支名
    
    jobs:
        build:
            runs-on: ubuntu-latest
    
        steps:
            - name: Checkout source
                uses: actions/checkout@v2
    
            - name: Setup Node.js
                uses: actions/setup-node@v2.1.5
                with:
                    node-version: '15.12.0'  # 建议使用 LTS 版本
    
            - name: Setup Environment
                run: |
                    git config --global user.email "<Email>"
                    git config --global user.name "<Username>"
                    sudo apt-get install -y pandoc
                    npm install -g hexo-cli
                    npm install
    
            - name: Custom file copy
                run: |
                    # 如果 NexT 主题通过 node_modules 安装，此步骤可用于覆盖默认模板文件
                    cp source/_data/post.njk_custom node_modules/hexo-theme-next/layout/_macro/post.njk
    
            - name: Hexo Deploy
                env:
                    GITHUB_TOKEN: ${{ secrets.HEXO_PUBLISH }}
                run: |
                    hexo clean
                    hexo g -d
    ```

7. **触发部署**：之后，只需将本地的源文件 `push` 到 GitHub 的私有备份仓库，即可自动触发部署。

> [!tip]
> 定期在本地更新 Hexo 插件及相关配置，并同步到备份仓库。

# CSS 样式自定义

在 `_config.next.yml` 文件中启用自定义样式文件：`custom_file_path.style: source/_data/styles.styl`。

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
    ul li li li { list-style: square; }

    ol li { list-style: decimal; }
    ol li li { list-style: lower-roman; }
}
```

## 更改列表与段落间距

```stylus
.post-body {
    p + ul,
    p + ol {
        margin-top: -1em;
    }
}
```

## 更改图片与图注间距

```stylus
.post-body {
    .image-caption, img + figcaption, .fancybox + figcaption {
        margin: 0px auto 15px;
    }
}
```

## 修改引用块样式

```stylus
.post-body {
    blockquote {
        border-left: 4px solid #92c1f0;
        background-color: rgb(245, 245, 245);
    }
}
```

# 常见问题解决

## MathJax 渲染问题

更换 Hexo 渲染器为 [Pandoc](https://pandoc.org/installing.html)。首先根据官网指引安装 Pandoc，然后更换 Hexo 渲染器：

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

## MathJax 3 换行问题

MathJax 3. x 版本不再默认支持使用 `\\` 进行自动换行（参见此 [GitHub Issue](https://github.com/mathjax/MathJax/issues/2312)）。

应遵守 LaTeX 规范的数学公式写法。请参考 [AMS (American Mathematical Society) 的标准规范](https://mirror.math.princeton.edu/pub/CTAN/info/short-math-guide/short-math-guide.pdf) 来书写多行公式，例如使用 `align` 或 `gather` 环境。

## 仅在文章摘要中显示封面图片

若希望封面图片仅显示在摘要，而非文章开头，可进行如下配置：

1. 修改 `_config.next.yml`：

    ```yaml
    excerpt_description: false
    ```

2. 修改主题模板文件 `/[themes_folder]/next/layout/_macro/post.njk`，q在 `{% if post.description and theme.excerpt_description %}` 与 `{% elif post.excerpt %}` 这两行之间，添加以下代码块：

    ```html
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

3. 使用方法：在 Markdown 文章的 front matter 中增加 `image: [image_path]`。此时，摘要将显示这张图片，无需再手动添加截断符 `<!-- more -->`。
4. 对于 `node_modules` 安装方式：为避免更新主题时修改被覆盖，可将修改后的 `post.njk` 文件保存至 `source/_data/post.njk_custom`。然后在 GitHub Action 的工作流中增加一个步骤，在部署时将此自定义文件复制到 `node_modules/hexo-theme-next/layout/_macro/` 目录下以覆盖原文件。

## 图注显示问题

在 `_config.next.yml` 中启用 `fancybox` 插件。插入图片时，使用 Markdown 的 `alt` 文本来指定图注：

```markdown
![这里是图注](test.png)
```

fancybox 会自动解析 `alt` 文本作为图注，并且有良好的渲染效果。图注在图片在放大后也会在下方显示。

关于图注与图片可能发生的重叠问题，参见[更改图片与图注间距](#更改图片与图注间距)

## iframe 或 PDF 无法加载

此问题通常是由于内嵌资源的 URL 使用了 `http://` 协议。请确保所有内嵌资源的链接都使用安全的 `https://` 协议。

## 避免 Nunjucks 语法冲突

Hexo 使用 Nunjucks 作为模板引擎，它会解析双大括号 `{{ }}`。如果在文章（尤其是在数学公式中）需要使用此语法，请使用模板标签将其包裹起来，以避免被错误解析。

## 其他可选插件

- 思维导图: [hexo-markmap](https://github.com/MaxChang3/hexo-markmap)
- 时间线: [hexo-tag-mdline](https://github.com/wangwei1237/hexo-tag-mdline)
- 图表绘制 (Chart.js): [hexo-tag-chart](https://github.com/Shen-Yu/hexo-tag-chart)
