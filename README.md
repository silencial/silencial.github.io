**基于 Hexo 和 NexT 生成 blog**

# Hexo

Hexo 是一个博客框架，使用 Markdown 解析文章生成静态网页。

通过 [Hexo 官方网址](https://hexo.io/zh-cn/docs/)进行安装与配置，安装完 Node.js 后使用命令行进行安装。

# NexT

NexT 是一个主题，可以运行在 Hexo 所搭建的 blog 上。

通过 [NexT 官方网址](https://theme-next.iissnan.com/)进行与 Hexo 的配置。

[NexT 的 github 主页](https://github.com/theme-next)，可在此进行下载各种扩展。

# 功能

## 文章内标题自动添加序号

安装 [hexo-heading-index](http://r12f.com/posts/adding-index-to-your-headings-with-hexo-heading-index/) 并在配置文件中取消侧边栏的自动编号，避免重复

## 加入搜索引擎和 sitemap

设置 [Google 站点管理工具](https://www.google.com/webmasters/tools/)的验证字符串，添加自己网址并导航到验证方法，选择`HTML 标签`，获取代码

```html
<meta name="google-site-verification" content="XXXXXXXXXXXXXXXXXXXXXXX" />
```

将`content`中的内容复制，修改主题配置文件，将内容粘贴至

```yaml
google_site_verification: XXXXXXXXXXXXXXXXXXXXXXX
```

提交站点地图：

首先下载插件`npm install hexo-generator-sitemap --save `,并在配置文件中设置`url`为自己网址，之后生成发布会新出现`sitemap.xml`文件，将其提交至 Google。

## 增加搜索功能

安装 Hexo 插件 [hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)，修改 `_config.yml` 文件生成数据库，并在 Next 设置中开启相应支持。

## 增加 RSS 支持

安装 Hexo 插件 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)，修改 `_config.yml` 文件，并在 Next 设置中开启 RSS 图标。

## 增加打赏

在 `next/source/images` 添加 `alipay.png` 和 `wechatpay.png`，并在 `_config.yml` 中设置

```yaml
reward_settings:
  enable: true
  animation: false
  comment: [comment]

reward:
  wechatpay: /images/wechatpay.png
  alipay: /images/alipay.png
```

适当裁剪和修改 `next/source/css/_common/components/post/post-reward.styl` 中的样式

## 增加分享按钮

使用 [AddThis Share](https://www.addthis.com)

## 代码可增加标题

```markdown
​```[language] [title] [url] [link-text]
code...
​```
```

- [language] 是代码语言的名称，用来设置代码块颜色高亮，非必须；
- [title] 是顶部左边的说明，非必须；
- [url] 是顶部右边的超链接地址，非必须；
- [link text] 是超链接的名称，非必须。

四项根据空格分隔，若中间想省略，则必须加`[]`

## 使用 NoteTag、Tabs、Label 标签

`Hexo`本身带有一些[Tags](https://github.com/iissnan/hexo-theme-next/pull/1697)。

用法查看[官方文档](https://theme-next.org/docs/tag-plugins/)。具体效果查看这篇 [blog](https://www.mulns.cn/posts/Tags-Plugins.html)。各种 Tag 的配置在`next/_config.yml`的`Tags Settings`中

## 使用图床（推荐）

选择 [Imgur](https://imgur.com/) 进行图片的托管，注册账号并下载 Chrome 插件 [imgur Community Extension](https://chrome.google.com/webstore/detail/imgur-community-extension/ehoopddfhgaehhmphfcooacjdpmbjlao) 进行图片上传和生成外链，之后复制到文本即可，且此时可以使用`width=80%`的操作，保证文章内图片宽度一致。

同时`imgur`内置支持六种不同尺寸的图片，例如原图片的地址为`https://i.imgur.com/test.jpg`，则六种尺寸的对应地址为：

- Huge Thumbnail 1024*1024: https://i.imgur.com/testh.jpg
- Large Thumbnail 640*640: https://i.imgur.com/testl.jpg
- Medium Thumbnail 320*320: https://i.imgur.com/testm.jpg
- Small Thumbnail 160*160: https://i.imgur.com/testt.jpg
- Big Square 160*160: https://i.imgur.com/testb.jpg
- Small Square 90*90: https://i.imgur.com/tests.jpg

**后两个选项将会对图片进行裁剪而非保持比例。**

## Emoji 支持

安装 [hexo-filter-emoji](https://github.com/theme-next/hexo-filter-emoji) 插件即可，使用语法和 Typora 相同，例如 `:smile:` 

## Github Action 自动部署

设置文件中包含个人私密信息，因此使用 private 仓库来保存。

在 `.gitignore` 文件中添加不需要跟踪的文件

```
.DS_Store
.idea/
*.log
*.iml
yarn.lock
package-lock.json
node_modules/
```

1. 在 Github 申请 `Settings -> Developer settings -> Personal access tokens`
2. 在仓库中 `Setting -> Secrets` 添加申请的 token，命名为 `[HEXO_PUBLISH]`
3. 新建 Github Action，内容如下

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
      - name: hexo deploy
        run: |
          export GITHUB_TOKEN="${{secrets.HEXO_PUBLISH}}"
          hexo g -d
```

# 问题

## 解决 Mathjax 渲染问题

更换渲染器为 [Pandoc](https://pandoc.org/installing.html)，首先在官网安装最新版，然后更换 Hexo 渲染器

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

## 使用 Mathjax 2

Mathjax 3 还没有实现 2 的全部功能。修改 `_config.next.yml`

```yaml
vendors:
  mathjax: https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.9/MathJax.js?config=TeX-MML-AM_CHTML
```

## 仅在摘要显示图片的各种方法

修改`next/_config.yml`：

```yaml
excerpt_description: false

auto_excerpt:
  enable: false
```

修改`/[next]/layout/_macro/post.njk`

```html
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

可以仅显示图片，此时不需要写`<!-- more -->`，或显示图片+摘要文字。

最后在`.md`文件的`Front-matter`中增加`image: [image_path]`

## 解决英文引号被解析成中文引号的问题

原因是使用了**智能标点**（smartypants）功能，对于不用的渲染器解决办法不同。`pandoc`下需要修改`/node_modules/hexo-renderer-pandoc/index.js`

```js
// 将下一语句
var args = [ '-f', 'markdown'+extensions, '-t', 'html', math]
// 替换为
var args = [ '-f', 'markdown-smart'+extensions, '-t', 'html-smart', math]
```

更多`pandoc`在`hexo`下的配置可参照该插件的 [README](https://github.com/wzpan/hexo-renderer-pandoc) 和 [官方文档](http://pandoc.org/MANUAL.html)。

## 图片标题问题

插入图片时

```markdown
![alt](123.png title="test")
```

`title`会被`next` 正常解析为图片标题，置于图片下方并居中；`alt`会被`pandoc`解析为`figcaption`，置于图片下方但不会被居中；fancybox 也会解析 `alt` 标签作为图片标题，并且有良好的格式，放大后也会在下方显示。

修改方法：使用 fancybox，只使用 `alt`，取消 pandoc 的渲染，在 `_config.yml` 中设置

```yaml
pandoc:
  extensions:
    - '-implicit_figures'
```

## 修改 CSS

在 `_config_next.yml` 中启用 `custom_file_path/style: source/_data/styles.styl`

### 更改列表嵌套序号样式

```css
.posts-expand .post-body {
	ul li { list-style: disc; }
	ul li li { list-style: circle; }
	ul li li li {list-style: square;}

	ol li { list-style: decimal;}
	ol li li {list-style: lower-roman;}
}
```

### 更改文章内链接 文本样式

```css
.post-body p, .post-body li {
  a {
    color: #4183C4;
    text-decoration: none;
    outline: none;
    border-bottom: 1px solid #4183C4;
    word-wrap: break-word;

    &:hover {
      color: $link-hover-color;
      border-bottom-color: $link-decoration-hover-color;
    }
  }
}

.post-body h1, h2, h3, h4 {
  a {
    color: #4183C4;
    text-decoration: none;
    outline: none;
    border-bottom: none;
    word-wrap: break-word;

    &:hover {
      color: $link-hover-color;
      border-bottom-color: $link-decoration-hover-color;
    }
  }
}
```

### 修改引用格式

```css
blockquote {
  border-left: 4px solid #4183C4;
  background-color: #f9f9f9;
  line-height: 1.8;
  font-size: 1.2em;
  font-family: "EB Garamond", Baskerville, Georgia, "Liberation Serif", "TW-Kai", "Kaiti SC", STKaiti, "AR PL UKai CN", "AR PL UKai HK", "AR PL UKai TW", "AR PL UKai TW MBE", "AR PL KaitiM GB", KaiTi, KaiTi_GB2312, DFKai-SB, "TW\-Kai", curve, serif;
}
```

# Future

1. 增加评论功能
2. gulp 进行压缩