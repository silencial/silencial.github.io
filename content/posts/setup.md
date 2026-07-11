---
title: Setup
date: 2020-02-25
updated: 2025-07-07
categories:
- Tech
tags:
- Tool
---

> [!warning]
> 正在完成从 Notion 到 Obsidian 的转移工作。完成后将修改本文 Notion 部分。

本文档旨在整理和记录我当前使用的各类工具及其相关配置。相关链接：

- [常用软件](https://silencial.notion.site/075d67decf434e138ff4bcbce93a9e26)：Notion 个人主页
- [CS Tools](/posts/cs-tool)：记录 CS 相关的常用工具

<!--more-->

---

**工作/摸鱼环境**：当前主力设备为 MBP 14" (M3 Pro)，外接一台 Dell U2720Q 4K 显示器。日常通过命令行或 VSCode SSH 连接至实验室的 Ubuntu 服务器。游戏需求则完全由 Xbox 满足。

之前使用过的设备，按照时间线顺序：

- Alienware 17.3" R4：大四时为了本科毕设购入，也是受了《生活大爆炸》中 Sheldon 的影响。安装了 Windows 和 Ubuntu 双系统，其中 Ubuntu 为主系统，Windows 仅用于 Steam 游戏。
- Surface Pro 5：来美国读 Master 时为了上课做笔记，同时又能运行桌面级软件选择的新欢。当时还没有投入 Apple 的怀抱。Typora + OneNote 也是陪伴了我的整个硕士学习生涯。后因进了西瓜汁阵亡。
- MBP 15.4" 2018：Surface Pro 阵亡后终于投入了 Apple 阵营，然后陆续加入了全家桶。

# 数字化记录方案

- 时间记录：使用 Toggl 进行手动时间追踪。尽管有时会忘记记录，但目前尚未找到更好的自动化方案。
- 影视记录：使用 VidHub 观影，其记录会自动同步至 Trakt。其他平台的观影则在 Trakt 手动记录。同时，我会将所有观影记录的标题手动录入 Notion，并通过 Notion API 自动抓取和填充来自豆瓣及 TMDB 的相关数据。
- 书籍记录：主要使用微信读书。由于缺乏类似 TMDB 这样数据结构完善且开放的书籍数据库，所有阅读记录均在 Notion 中手动录入。
- 音乐记录：使用 Apple Music。若曲库中不存在某首歌曲，我会从其他渠道下载并手动上传至 iCloud 音乐资料库。
- 播客记录：使用 Pocket Casts。
- 个人学习笔记：在本地使用 Markdown 撰写，通过 Git 同步至 GitHub，再利用自动化流程发布为静态网站。具体搭建过程可参照 [Hugo Blog Setup](/posts/hugo-blog-setup)。
- 论文记录：采用 Zotero 结合 Notion 的方式进行管理。
- 运动记录：通过 Apple Watch 追踪。攀岩活动使用 Redpoint App 记录，其他运动则使用系统自带的「体能训练」App。
- 其他记录：如日记、学习心得、工具收藏、购物清单等杂项，均在 Notion 中手动记录。

# 系统配置

## Dock

缩短 Dock 自动隐藏和显示的延迟：

```bash
defaults write com.apple.dock autohide-delay -float 0; killall Dock
```

## Quick Look 插件

- [QLMarkdown](https://github.com/toland/qlmarkdown)
- [SyntaxHighlight](https://github.com/sbarex/SourceCodeSyntaxHighlight)

# 应用

## [Alfred](https://www.alfredapp.com/)

强大的 Spotlight 替代品，可用于快速启动软件、查找文件、搜索网页、管理书签和查看剪贴板历史。其核心优势在于可通过 Workflows 进行功能扩展。

推荐的 Workflows：

- [Apple Music](https://github.com/gustavosaez/Alfred-Workflow-Apple-Music)：快速打开 Apple Music 中的心情或场景歌单。
- [Calculate Anything](https://github.com/biati-digital/alfred-calculate-anything)：集成了计算器、单位转换、货币换算等多种功能。
- [Calendar++](https://github.com/zeitlings/alfred-calendar)：与 macOS 自带的日历应用进行交互。
- [Colors](https://www.packal.org/workflow/colors)：通过 RGB, HSL, Hex 值搜索并拾取颜色。
- [Douban](https://github.com/h3l/douban-workflow)：搜索豆瓣上的书籍和电影并显示其评分。
- [Menu Bar Search](https://github.com/BenziAhamed/Menu-Bar-Search)：搜索当前激活应用的菜单栏选项。
- [OCR](https://github.com/zeitlings/alfred-workflows)：对截图进行 OCR 文字识别。
- [Reminders for Alfred](https://github.com/surrealroad/alfred-reminders/releases)：支持通过自然语言快速添加提醒事项。
- [Search ALL the docs!](https://alfred.app/workflows/chrisgrieser/search-all-the-docs/)：用于搜索各类开发文档，并集成了一些实用小工具。
- [Shrieking Chimes](https://alfred.app/workflows/vitor/shrieking-chimes/)：便捷地设置闹钟和倒计时。
- [Toggl](https://github.com/jason0x43/alfred-toggl)：与 Toggl Time Tracker 进行交互，方便追踪时间。
- [Word Search](https://github.com/isaacpz/Alfred-WordSearch)：查询同义词、反义词、押韵词和相关形容词。
- [Youdao translate](https://github.com/wensonsmith/YoudaoTranslate)：快速进行中英单词互译。

配置自动备份（⚠️ 注意：配置中可能包含敏感信息）：`Preferences -> Advanced -> Set preferences folder`。

## [Dia](https://www.diabrowser.com/)

新型 AI 浏览器。深度集成 AI 功能，让 AI 成为与互联网交互的核心界面。

## [Folo](https://folo.is/)

信息聚合工具。在 RSS 订阅服务的基础上扩展了更多功能，支持订阅网站、社交媒体、视频平台等多种信息源，从而实现信息的集中管理。

## [iTerm2](https://www.iterm2.com/)

macOS 平台上一款功能强大的终端模拟器。

一些个人设置：

- 设为默认终端：`iTerm2 -> Make iTerm2 Default Term`。
- 全局快捷键：`iTerm2 -> Settings -> Keys -> Hotkey`。我的设置为 ⌥ + Space。
- 字体配置：为正常显示 Powerline 和 Nerd Font 图标，需安装 [Nerd Fonts](https://www.nerdfonts.com/) 字体。在 `iTerm2 -> Settings -> Profiles -> Text` 中选择相应字体，推荐 [Maple Mono NF CN](https://github.com/subframe7536/maple-font)。
- 配色方案：`iTerm2 -> Settings -> Profiles -> Colors`。可从 iTerm 2 的[配色方案库](https://github.com/mbadolato/iTerm2-Color-Schemes)下载。推荐 `GruvboxDark` 和 `Penumbra Dark++`。

备份：

- 自动备份设置：`Settings -> General -> Settings`。
- 手动备份 Profiles：`Settings -> Profiles -> Other Actions -> Save all Profiles as JSON`。

技巧：

- Tmux 集成：使用 `tmux -CC` 命令与 iTerm 2 进行深度集成。在 SSH 远程会话中尤其有用，可以原生支持滚动和复制粘贴等操作。
- 分屏：⌘D 竖直分屏；⇧⌘D 水平分屏。
- 智能选中：双击选中单词，三击选中整行，四击可智能选中 URL 或路径。
- 输出文本触发器：`Settings -> Profiles -> Advanced -> Triggers` 支持使用正则表达式匹配输出来触发特定动作。
- 命令完成时提示：⌥⌘A 可在当前 shell 命令执行完毕后发送通知。
- 打开命令编辑器：⇧⌘. 方便输入和编辑长命令。

## [Karabiner](https://karabiner-elements.pqrs.org/)

macOS 键盘映射软件。通过在 `~/.config/karabiner/assets/complex_modifications/` 路径下添加 JSON 文件并编写规则，即可实现自定义映射。

我的主要配置：

- 定义 `Caps Lock` 键：单独按下时映射为 `Esc`，与其他键组合时则作为 `Hyper` 键（⌃⌥⌘⇧）。
- Vim 式方向键：`Hyper` 键与 `h, j, k, l` 组合映射为 `←, ↓, ↑, →`，并兼容其他修饰键。
- 其他 `Hyper` 配置：`Hyper` 键与 `Tab` 组合映射为 `Ctrl + Tab`。
- 应用切换：右 `Command` 键与不同字母键组合用来打开不同应用。

## [Lunar](https://github.com/alin23/Lunar)

能够利用 MacBook 内置的光线传感器，自动调节外接显示器的亮度和对比度。其界面美观，提供多种调节模式，可根据实际需求调整参数。

## [Mathpix Snip](https://mathpix.com/)

通过截图快速将数学公式转换为 LaTeX 语法，识别准确率高，在用 Markdown 撰写学习笔记时尤其方便。

## [Obsidian](https://obsidian.md/)

个人第二大脑，用于构建我的个人知识库。

## [Pearcleaner](https://github.com/alienator88/Pearcleaner)

免费开源的 Mac 软件卸载工具。

## [Rime](https://rime.im/)

全平台、开源的输入法引擎，其在 macOS 上的实现是鼠须管 (Squirrel)。提供了丰富的定制化选项，但上手曲线较为陡峭，建议参考官方[帮助文档](https://rime.im/docs/)。所有用户数据均存储在本地，其设置和词库支持跨平台同步。

目前使用的配置方案：[rime-ice](https://github.com/iDvel/rime-ice)。

## [Typora](https://typora.io/)

所见即所得的 Markdown 编辑器，我主要用它来做笔记和撰写博客文章。

主题推荐：[Drake](https://theme.typora.io/theme/Drake/) 和 [dyzj](https://theme.typora.io/theme/dyzj/)

## [VSCode](https://code.visualstudio.com/)

代码编辑器。插件生态丰富，迭代迅速。

一些推荐插件

- Bookmarks：为代码行添加书签，方便快速跳转。
- Catppuccin for VSCode：一款流行的主题。
- Compare Folders：可视化地对比两个文件夹的差异。
- Data Wrangler：在 Jupyter Notebook 中提供强大的数据查看和清洗工具。
- Everforest：一款护眼主题。
- GitHub Copilot：AI 代码补全工具，功能强大。
- GitLens：在编辑器中无缝集成 Git 信息，例如代码的作者和修改历史。
- Markdown All in One：提供全面的 Markdown 编辑支持。
- Markdown Preview Enhanced：Markdown 预览增强，我主要用于更换 MathJax 作为 LaTeX 渲染引擎。
- markdownlint：用于检查 Markdown 文件的语法和风格。
- Material Icon Theme：包含一套美观的文件图标。
- paste-Indent：粘贴代码时能更好地保持原有的缩进格式。
- Path Intellisense：自动补全文件路径。
- Python Indent：修正 Python 代码的自动缩进问题。
- Rainbow CSV：为 CSV 文件提供彩虹高亮，提升可读性。
- Ruff：用于检查 Python 文件的语法和风格。
- Tabster：将打开的标签页保存为工作组，方便在不同项目间切换。
- Todo Tree：高亮显示代码中的 `TODO`、`FIXME` 等关键字，并将其聚合在侧边栏方便查看。

## [Zotero](https://www.zotero.org/)

强大的知识管理与文献引用工具，我主要用它来整理学术文献。我使用 [Koofr](https://app.koofr.net) 作为 WebDAV 服务器进行附件同步，其免费版提供 10 GB 存储空间。

常用插件：

- [Add-on Market for Zotero](https://github.com/syt2/zotero-addons?tab=readme-ov-file#readme)：用于在 Zotero 内直接浏览、安装和评论插件。
- [Notero](https://github.com/dvanoni/notero)：将 Zotero 数据库同步至 Notion，方便在 Notion 中整理和撰写文献笔记。
- [Google Scholar Citation Count](https://github.com/justinribeiro/zotero-google-scholar-citation-count)：从 Google Scholar 抓取并显示论文的引用次数。

# 命令行工具

## [autojump](https://github.com/wting/autojump)

根据使用频率学习目录，实现快速跳转。

## [bat](https://github.com/sharkdp/bat)

`cat` 的增强版，支持语法高亮和 Git 集成。

## [bottom](https://github.com/ClementTsang/bottom)

一款跨平台的图形化系统监控仪表。

## [delta](https://github.com/dandavison/delta)

一款功能丰富的 `diff` 查看工具，常用于增强 `git diff` 的输出效果。

## [duf](https://github.com/muesli/duf)

`df` 的替代品，以更美观、友好的方式显示磁盘使用情况。

## [fastfetch](https://github.com/fastfetch-cli/fastfetch)

快速获取系统信息并以美观的方式展示，可定制化选项多。

## [fd](https://github.com/sharkdp/fd)

`find` 的替代品，提供了更简洁的语法和更快的搜索速度。

## [fzf](https://github.com/junegunn/fzf)

通用的命令行模糊搜索工具，可与多种命令（如 `history`、`find`）结合使用。

## [htop](https://github.com/htop-dev/htop)

`top` 的增强版，提供了一个彩色的、可交互的进程管理界面。

## [lsd](https://github.com/Peltoche/lsd)

`ls` 的增强版，增加了颜色、图标等现代化的显示效果。

## [Neovim](https://neovim.io/)

新一代的 Vim 编辑器，在继承 Vim 优点的基础上提供了更强的扩展性。新手可通过 `vimtutor` 命令快速入门。Neovim 的学习曲线较为陡峭，但掌握后能极大提升编辑效率。

- 插件管理：使用 [vim-plug](https://github.com/junegunn/vim-plug)。可在 [VimAwesome](https://vimawesome.com/) 搜索插件。
- LSP 支持：使用 [coc.nvim](https://github.com/neoclide/coc.nvim) 提供 LSP 支持，带来代码补全、定义跳转等功能。

> [!todo]
> 尝试 LazyVim

## [nvidia-htop](https://github.com/peci1/nvidia-htop)

`nvidia-smi` 的增强版，以类似 `htop` 的界面友好地显示 GPU 使用情况。

## [Oh My Zsh](https://ohmyz.sh/)

一个流行的 Zsh 配置管理框架。

启用的自带插件：

- `git`：提供了大量 `git` 命令的别名。
- `autojump`：配合 `autojump` 工具，解决路径自动跳转问题。
- `extract`：使用统一的 `x` 命令解压各种格式的压缩文件。

其它插件：

- [Powerlevel10k](https://github.com/romkatv/powerlevel10k)：功能强大、高度可定制的主题，提供向导式配置。
- [zsh-you-should-use](https://github.com/MichaelAquilina/zsh-you-should-use)：当输入的命令存在更简洁的别名时进行提示。
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)：根据历史记录提供命令建议，按右方向键即可补全。
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)：为命令行输入提供实时语法高亮（注意：此插件应放在 `.zshrc` 插件列表的最后）。

## [Ranger](https://github.com/ranger/ranger)

基于命令行的文件管理器，采用类似 Vim 的快捷键进行操作。支持高度自定义的快捷键和命令。建议新手在使用前阅读官方 [Wiki](https://github.com/ranger/ranger/wiki)。

## [ripgrep](https://github.com/BurntSushi/ripgrep)

`grep` 的替代品，搜索速度极快，且默认会忽略 `.gitignore` 中指定的文件。

## [Tealdeer](https://tealdeer-rs.github.io/tealdeer/)

`tldr` 的一个实现版本。可在命令行中显示命令的速查表，支持自定义。

## [Tmux](https://github.com/tmux/tmux)

强大的终端复用器 (Terminal Multiplexer)。允许用户在单个终端窗口中创建和管理多个会话，即使 SSH 连接断开，会话及其中的进程也会在后台继续运行。每个会话可以包含多个窗口，每个窗口又可以划分为多个窗格。会话中的操作在服务器端和客户端是同步可见的，非常适合结对编程或进行远程演示。

- 插件管理：安装 [tpm](https://github.com/tmux-plugins/tpm) (Tmux Plugin Manager)。
- 主题：安装 [Dracula Tmux](https://github.com/dracula/tmux) 主题插件，定制化简单。

### vim + tmux + true_color + italic

1. 在 `.tmux.conf` 中添加如下配置：

    ```tmux
    set -g default-terminal tmux-256color
    set -ga terminal-overrides ",xterm-256color:RGB"
    ```

2. 下载 [tmux-256color](https://gist.github.com/nicm/ea9cf3c93f22e0246ec858122d9abea1) terminfo 文件，然后在终端执行 `/usr/bin/tic -x tmux-256color`。该命令会在 `~/.terminfo` 目录下生成编译后的文件。

### PATH 路径问题

⚠️ 注意：在 Tmux 会话中，`$PATH` 环境变量的值可能与常规终端会话中不同。

## [xclip](https://github.com/astrand/xclip)

Linux 系统下的命令行剪贴板工具。macOS 自带功能类似的 `pbcopy` 和 `pbpaste`。

## [Yabai](https://github.com/koekeishiya/yabai)

macOS 下的平铺式窗口管理器。配合 skhd 热键工具和 sketchybar 状态栏，可以实现高度定制化的桌面环境，显著提升窗口管理的效率，减少对鼠标或触控板的依赖。建议在安装与使用前完整阅读官方 [Wiki](https://github.com/koekeishiya/yabai/wiki)。

一些必要的系统设置（路径均在 `System Settings`）：

- 防止桌面顺序自动更改：取消勾选 `Desktop & Dock -> Mission Control -> Automatically rearrange Spaces based on most recent use`。
- 隐藏 Dock 栏：勾选 `Desktop & Dock -> Dock -> Automatically hide and show the Dock`。
- 隐藏菜单栏：将 `Control Center -> Menu Bar Only -> Automatically hide and show the menu bar` 设置为 `Always`。
- 减少动画效果：勾选 `Accessibility -> Display -> Reduce motion`。

### [Sketchybar](https://github.com/FelixKratz/SketchyBar)

轻量级状态栏，配合 Yabai 使用，显示当前桌面和窗口等信息。其安装和配置过程相对简单。

### [Skhd](https://github.com/koekeishiya/skhd)

简单的热键守护进程，用于通过快捷键触发命令，配合 Yabai 使用。其配置文件规则简单，并可与 Karabiner 等键盘映射软件结合，实现更丰富的定制化需求。

## [Yadm](https://yadm.io/)

> Yet Another Dotfiles Manager.

Dotfiles 管理工具。其基本工作原理是在用户的 Home 目录（`~`）下创建一个 Git 仓库。命令基本与 Git 保持一致，但工作模式相反：Yadm 默认不会追踪任何文件，需要通过 `yadm add` 命令手动添加需要管理的文件。

## [you-get](https://you-get.org/)

从各种在线网站下载媒体内容。

# 浏览器插件

## [浸入式学语言助手](https://github.com/xiao-zaiyi/illa-helper)

使用 AI 大模型对当前网页内容进行分析，智能选择适合用户水平的词汇进行翻译。旨在创造沉浸式的外语学习环境，帮助用户在阅读原生内容时无缝学习新词汇，提升语言能力。

## [BewlyBewly](https://github.com/BewlyBewly/BewlyBewly)

为哔哩哔哩 (Bilibili) 设计的开源扩展，旨在提供一个更简洁、更具沉浸感的观看体验。重塑了用户界面，并提供了丰富的自定义选项，如夜间模式、简化首页等功能。

## [cat-catch](https://github.com/xifangczy/cat-catch)

网页资源嗅探工具，能够检测并捕获网页中加载的视频、音频、图片等媒体资源，方便用户一键下载或保存网络上的流媒体内容。

## [Control Panel for YouTube](https://soitis.dev/control-panel-for-youtube)

为 YouTube 提供额外的控制选项和用户界面改进。

## [Decentraleyes](https://decentraleyes.org/)

专注于隐私保护的插件，通过拦截指向大型内容分发网络 (CDN) 的流量，转而从本地提供常用的库文件。这可以有效防止第三方通过 CDN 对你的网络行为进行追踪。

## [iCloud Passwords](https://chromewebstore.google.com/detail/icloud-passwords/pejdijmoenmkgeppbflobdenhhabjlaj)

苹果官方的浏览器插件。将你在苹果设备上创建和保存的密码同步至浏览器，实现了跨平台的无缝密码管理。

## [Obsidian Web Clipper](https://obsidian.md/clipper)

Obsidian 网页剪藏工具，可以将网页内容（包括全文、选中部分或链接）快速保存为 Markdown 文件到 Obsidian 库中。支持自定义模板、元数据，并支持使用 AI 大模型处理网页内容。

## [PocketTube](https://pockettube.io/)

强大的 YouTube 订阅管理工具。允许你将订阅的频道分类整理到不同的集合中，进行分组管理。

## [Surfingkeys](https://github.com/brookhong/Surfingkeys)

浏览器插件，实现了类似 Vim 的键盘操作模式来浏览网页。上手简单、功能齐全，支持自定义快捷键。

## [Tampermonkey](https://www.tampermonkey.net/)

用户脚本 (Userscript) 管理器。允许用户在任意网页上运行自定义的 JavaScript 代码。通过安装来自 Greasy Fork 等社区的脚本，可以实现去除广告、增强网站功能、优化页面布局等无数种自定义效果。

## [uBlock Origin](https://github.com/gorhill/uBlock)

高效且轻量化的开源内容拦截器，以其极低的 CPU 和内存占用而著称。可以通过订阅不同的过滤列表来有效屏蔽追踪器、恶意软件域名等多种网络威胁。

## [Video Speed Controller](https://github.com/igrigorik/videospeed)

为网页中的 HTML5 视频播放器增加精细的速度控制功能。允许用户通过快捷键方便地进行加速/减速、前进/回退操作。

## [Wikiwand](https://www.wikiwand.com/)

将 Wikipedia 的传统界面替换为一个现代化、更易于阅读的全新版式。

## [Zotero Connector](https://www.zotero.org/download/connectors)

Zotero 官方的浏览器插件。能自动识别网页上的文献信息，并允许一键将其元数据、PDF 或网页快照直接保存到 Zotero 资料库中。

# 系统相关

## Win & Ubuntu 双系统

### Win 安装 Ubuntu

1. 进入 Windows 的电源选项，关闭「快速启动」功能。
2. 使用「磁盘管理」工具，从现有分区中压缩出一块未分配的可用空间（建议 100 GB 以上）。
    - ==注意：若硬盘为动态磁盘，必须先使用[傲梅分区助手](https://www.disktool.cn/)等工具将其转换为基本磁盘，否则无法安装。==
3. 在 Ubuntu [官网](https://ubuntu.com/download/desktop)下载桌面版镜像文件，并参照官方教程制作启动 U 盘。
4. 将 U 盘插入电脑，重启并进入 BIOS/UEFI 设置，选择从 U 盘启动。
5. 按照安装向导的步骤进行。在「安装类型」页面，选择与 Windows 共存的安装选项，安装程序会自动处理分区。
    - 许多旧教程可能仍在介绍基于 Legacy 启动模式、需要关闭安全启动 (Secure Boot) 并手动分区的复杂流程。实际上，当前版本的 Ubuntu 安装程序已大大简化了该过程，无需进行这些繁琐操作。
6. 安装完成后，在 `Software & Updates -> Additional Drivers` 中安装 Nvidia 显卡驱动。如果安装时未关闭 Secure Boot，系统会提示设置一个密码。重启后，在蓝色的 MOK 管理界面中选择 `Enroll MOK` 或 `Enroll Key`，并输入之前设置的密码以完成驱动签名。

## Wake-on-Lan (WOL)

==前提：确认主板在硬件上支持 WOL 功能。==

服务端配置（被唤醒的机器）：

- Ubuntu：
    1. 在 BIOS/UEFI 中开启 WOL (Wake on LAN) 选项。
    2. 安装 ethtool：`sudo apt-get install ethtool`。
    3. 开启网卡的 WOL 服务：`sudo ethtool -s [INTERFACE_NAME] wol g`（将 `[INTERFACE_NAME]` 替换为你的有线网卡名称，如 `eth0` 或 `enp3s0`）。
- Mac：`System Settings -> Battery -> Options -> Wake for network access`。

客户端配置（发送唤醒指令的机器）：

- 安装 `wakeonlan`
- 发送魔术包以唤醒目标机器：`wakeonlan -i [BROADCAST_IP] -p [PORT] [MAC_ADDRESS]`

# Tips

## SSH With Tmux

使用 `ssh USER@HOST -t 'tmux new -A -s main'` 命令，可以在建立 SSH 连接时自动 attach 到名为 `main` 的 Tmux 会话。如果该会话不存在，则会自动创建。

## History 增加时间显示

在 `.zshrc` 中添加 `HIST_STAMPS="yyyy-mm-dd"` 即可为历史命令增加时间戳。

## Mac System Copy

`pbcopy` 只能够复制文本内容。为了实现类似 Finder 中的「拷贝」功能（可用于粘贴文件），可以将下列函数加入 `.zshrc`：

```bash
# Copy a file to the system clipboard
syscp() {
    osascript -e 'on run {a}' -e 'set the clipboard to posix file a' -e end "$(readlink -f -- "$1")"
}
```

通过 `syscp FILE` 来复制文件。注意此函数仅支持复制单个文件。
