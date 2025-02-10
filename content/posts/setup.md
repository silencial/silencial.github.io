---
title: Setup
date: 2020-02-25
lastmod: 2023-10-12
categories:
- Tech
tags:
- Tool
cover:
   image: https://i.imgur.com/3XR2qVc.jpg
---

收集当前使用的工具及配置。其它相关 Blog:

- [常用软件](https://silencial.notion.site/cb9a9089382f4f76ba1d82ba3e50ec3f?v=15faca853b4a45d3a085a8238f6220b4)：Notion 个人主页
- [CS Tools]({{< ref "cs-tools" >}})：记录 CS 中常用工具

<!--more-->

---

**工作/摸鱼环境**：当前使用的是 MBP 14" M3 Pro + Dell 2720Q 4K 显示器。时常会命令行或 VSCode ssh 到 Lab 的 Ubuntu 下。之前有过一台 Alienware 17" R4 安装了 Windows&Ubuntu 双系统。Ubuntu 为主系统，Windows 只用来 Steam。由于需要搬家 + 感觉 MBP 买了不用有些浪费所以卖掉了。目前没有配台式的打算，游戏环境完全交给 Xbox。

# 系统配置

## Dock

快速移入移出

```bash
defaults write com.apple.dock autohide-delay -float 0; killall Dock
```

## Quick Look 插件

- [QLMarkdown](https://github.com/toland/qlmarkdown)
- [SyntaxHighlight](https://github.com/sbarex/SourceCodeSyntaxHighlight)

# [Yabai](https://github.com/koekeishiya/yabai)

macOS 下平铺式窗口管理器，搭配 skhd 热键守护进程 + sketchybar 状态栏实现桌面环境大改造。快速切换、排列窗口及桌面，告别鼠标和触控板。安装与使用前请完整阅读 [wiki](https://github.com/koekeishiya/yabai/wiki)。

一些系统设置（均在 `System Preferences`）：

- 防止桌面顺序自动更改：取消勾选 `Mission Control -> Automatically rearragen Spaces based on most recent use`
- 减少动画效果：勾选 `Accessibility -> Display -> Reduce Motion`
- 隐藏 Dock：勾选 `Dock -> Automatically hide and show the Dock`
- 隐藏菜单栏：勾选 `General -> Automatically hide and show the menu bar`

## [Sketchybar](https://github.com/FelixKratz/SketchyBar)

一款轻量状态栏，配合 Yabai 使用，显示当前桌面和窗口信息。安装和配置都比较简单

## [Skhd](https://github.com/koekeishiya/skhd)

简单的热键守护进程，快捷键触发命令，配合 Yabai 使用。配置文件规则简单，搭配 Karabiner 键盘映射软件可以实现更多定制化需求

# [Karabiner](https://karabiner-elements.pqrs.org/)

macOS 键盘映射软件。添加 `~/.config/karabiner/assets/complex_modifications/custom.json` 文件并编写规则即可。

- 定义 `⇪` 键单独按下时为 Esc，与其它键组合时变为 Hyper
- 与 h, j, k, l 组合变为上下左右，并兼容其它修饰键

# [Alfred](https://www.alfredapp.com/)

替换 Mac Spotlight，功能强大。可用来打开软件、文件、网页；搜索书签；查看剪贴板。另外可以添加 Workflows 扩展功能。推荐 Workflow：

- [Apple Music Playlists](https://github.com/gustavosaez/Alfred-Workflow-Apple-Music)：打开 Apple Music 心情/场景歌单
- [Calculate Anything](https://github.com/biati-digital/alfred-calculate-anything)：计算器、单位转换、货币换算、等等
- [Colors](https://www.packal.org/workflow/colors)：通过 RGB，HSL，Hex 搜索颜色
- [Douban](https://github.com/h3l/douban-workflow)：搜索豆瓣上的书籍和电影并显示评分
- [Menu Bar Search](https://github.com/BenziAhamed/Menu-Bar-Search)：搜索当前 App 下的菜单选项
- [Reminders for Alfred](https://github.com/surrealroad/alfred-reminders/releases)：自然语言添加 Reminders 事项
- [Toggl](https://github.com/jason0x43/alfred-toggl)：和 Toggl Time Tracker 互动
- [Word Search](https://github.com/isaacpz/Alfred-WordSearch)：搜索近义词、反义词、押韵词、形容词
- [Youdao translate](https://github.com/wensonsmith/YoudaoTranslate)：中英单词翻译

自动备份设置（存在敏感信息）：`Preferences -> Advanced -> Set preferences folder`

# [Lunar](https://github.com/alin23/Lunar)

根据 Macbook 的自带光传感器自动调节外接显示器亮度和对比度。界面好看，模式种类多，需要自己根据需要调节一些参数

# [Rime](https://rime.im/)

全平台开源输入法，在 macOS 下为鼠须管。可定制化选项多，上手比较复杂，参考[帮助文档](https://rime.im/docs/)。全部数据都在本地，设置和词库可以跨平台同步。

目前使用配置：[rime-ice](https://github.com/iDvel/rime-ice)

# [Typora](https://typora.io/)

Markdown 编辑器，平时用来做笔记，写 Blog。Typora 天下第一 :dog:

主题推荐 [Drake](https://theme.typora.io/theme/Drake/) 和 [dyzj](https://theme.typora.io/theme/dyzj/)

# [Zotero](https://www.zotero.org/)

知识管理平台，主要用来整理文献。使用 [Koofr](https://app.koofr.net) 作为 WebDAV 同步免费版有 10G 空间

插件：

- [Notero](https://github.com/dvanoni/notero)：同步 Zotero 数据库至 Notion 中，方便做笔记
- [Google Scholar Citation Count](https://github.com/justinribeiro/zotero-google-scholar-citation-count)：从 Google Scholar 获取论文引用量
- [Zotfile](https://github.com/jlegewie/zotfile)：文件管理，包括重命名，移动等

# [Mathpix Snip](https://mathpix.com/)

截图数学公式转化 Mathjax 语法，准确率高，用 Typora 写学习笔记时非常方便

# [Surfingkeys](https://github.com/brookhong/Surfingkeys)

浏览器插件，键盘操作网页，按键模式与 Vim 类似。上手简单，功能齐全，支持自定义

# [VSCode](https://code.visualstudio.com/)

代码编辑器，插件生态全，迭代更新快，又是一个天下第一系列 :dog:

一些推荐插件

- Bookmarks：给行添加书签，方便跳转
- Code GPT：GPT 侧栏
- Everforest：主题
- Git Graph：查看 Git Graph
- GitHub Copilot：AI 代码补全，强到离谱
- GitLens：在文件中显示 git 信息
- Material Theme：主题，主要用它来显示文件图标
- Paste-Indent：保持缩进格式粘贴
- Path Intellisense：自动补全文件路径
- Python Indent：解决一些 Python 自动缩进问题
- Tabster：保存标签页至工作组
- Todo Tree：高亮关键字

# [iTerm2](https://www.iterm2.com/)

macOS 终端工具。一些设置：

- 默认终端：`iTerm2 -> Make iTerm2 Default Term`
- 快捷键：`iTerm2 -> Preferences -> Keys -> Hotkey`。设置为 `⌥Space`
- 字体问题：安装 [Nerd Fonts](https://www.nerdfonts.com/)，在 `iTerm2 -> Preferences -> Profiles -> Text` 中选择字体。推荐 `14 pt CaskaydiaCove Nerd Font Regular`
- 下载 iTerm2 的[配色方案](https://github.com/mbadolato/iTerm2-Color-Schemes)。推荐 `Snazzy`, `ayu`

备份：

- 自动备份设置：`Preferences -> General -> Preferences -> Load preferences from a cutom folder or URL`
- 手动备份 Profiles：`Preferences -> Profiles -> Other Actions -> Save all Profiles as JSON`

技巧：

- Tmux 集成：使用 `tmux -CC`，在 ssh 时尤其好用，比如滚动和复制粘贴
- 分屏：`⌘D` 竖直分屏；`⇧⌘D` 水平分屏
- 智能选中：双击选中，三击选中整行，四击智能选中
- 输出文本触发器：`Preferences -> Profiles -> Advanced -> Triggers` 支持正则表达式
- 命令完成时提示：`⌥⌘A`
- 打开命令编辑器：`⇧⌘.`

# [Oh My Zsh](https://ohmyz.sh/)

zsh 增强。开启一些自带插件：

- git：关于 `git` 的各种 alias
- autojump：安装 autojump 之后路径问题
- extract：使用 `x` 傻瓜式解压文件

其它插件：

- [Powerlevel10k](https://github.com/romkatv/powerlevel10k)：主题，可定制化选项多，傻瓜式引导配置
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)：根据历史提示命令，一键补全
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)：高亮命令（注意需要放在 `.zshrc` 插件列表最后一位）

# [Neovim](https://neovim.io/)

新一代 Vim 命令行编辑器

新手使用 `vimtutor` 命令来快速熟悉，学习曲线陡峭。

安装 [vim-plug](https://github.com/junegunn/vim-plug) 插件管理器；插件可以在 [VimAwesome](https://vimawesome.com/) 中搜索

使用 [coc.nvim](https://github.com/neoclide/coc.nvim) 提供 LSP 等多种支持

# [Tmux](https://github.com/tmux/tmux)

终端复用器，ssh 连接断开时会话依旧会保存。每个会话可以打开多个窗口，每个窗口可以划分窗格。

一个会话中的操作对服务器端和用户端同时可见，可以用来做演示。

- 安装 [tpm](https://github.com/tmux-plugins/tpm) 插件管理器
- 安装 [Dracula Tmux](https://github.com/dracula/tmux) 主题插件，定制化简单

## vim + tmux + true_color + italic

1. `.tmux.conf` 中加入

   ```tmux
   set -g default-terminal tmux-256color
   set -ga terminal-overrides ",xterm-256color:RGB"
   ```

2. 下载 [tmux-256color](https://gist.github.com/nicm/ea9cf3c93f22e0246ec858122d9abea1) 后终端执行 `/usr/bin/tic -x tmux-256color` 后会产生 `~/.terminfo` 文件

## PATH 路径问题

<mark>注意 Tmux 下的 PATH 值可能会发生变化</mark>

# [Ranger](https://github.com/ranger/ranger)

命令行文件管理器，按键模式和 Vim 类似。支持自定义快捷键和命令。新手请阅读 [wiki](https://github.com/ranger/ranger/wiki)

<mark>Bug</mark>：快速预览有时会卡死，需要 `ctrl+c` 后继续。参考 [issue](https://github.com/ranger/ranger/issues/1787) 解决方法

# [Yadm](https://yadm.io/)

> Yet Another Dotfiles Manager.

Dotfiles 管理工具。基本工作原理即是在 `~` 目录下建立一个 git 仓库，命令与 git 相同，模式与 git 相反，即默认不添加文件，需要手动 `yadm add`

# 命令行工具

- [autojump](https://github.com/wting/autojump)：目录快速跳转
- [bat](https://github.com/sharkdp/bat)：`cat` 增强版，输出高亮，多种语法
- [cheat](https://github.com/cheat/cheat)：提示命令的用法，可以自己编写
- [fzf](https://github.com/junegunn/fzf)：模糊搜索，可以用来搜索文件、历史命令、等等
- [htop](https://github.com/htop-dev/htop)：交互式进程管理器
- [icdiff](https://github.com/jeffkaufman/icdiff)：支持高亮的比对工具，可以搭配 git 使用
- [lsd](https://github.com/Peltoche/lsd)：`ls` 增强版
- [neofetch](https://github.com/dylanaraps/neofetch)：显示系统信息，可定制选项多
- [nvidia-htop](https://github.com/peci1/nvidia-htop)：`nvidia-smi` 增强版，显示进程对应用户与 CPU，Memory 使用率
- [ripgrep](https://github.com/BurntSushi/ripgrep)：类似于 `grep`，使用正则表达式搜索文件内容
- tree：树形显示文件夹结构
- [xclip](https://github.com/astrand/xclip) (Linux)：复制文件内容至剪切板，Mac 自带 `pbcopy`

# 个人记录

如何将个人生活数字化

- 时间记录：使用 Toggl 手动记录。经常忘记但好像没什么好方法

- 影视记录：Infuse 可以将观影记录同步至 Trakt。其它观看则在 Trakt 手动记录。同时所有影视标题手动加入 Notion，且调用 Notion API 自动搜索豆瓣和 TMDB 数据并填入
- 书籍记录：使用微信读书，如果找不到书籍则自己寻找并上传。由于读书频率不高，因此都在 Notion 手动记录（也是因为没有一个类似 TMDB 的书籍网站可以爬数据）
- 音乐记录：使用 Apple Music，如果歌曲不存在则从其它地方下载并上传。
- 播客记录：使用 Pocket Cast

- 个人学习笔记：本地写 markdown 文件，同步至 Github 自动发布静态网页。搭建参照 [Hugo 博客搭建](https://silencial.github.io/posts/hugo-blog-setup/)
- 论文记录：使用 Zotero + Notion
- 运动记录：Apple Watch 记录。主要攀岩，使用 Redpoint，其它则用自带训练
- 其它记录：日记、学过的东西、有用的工具、购买的物品等则手动记录至 Notion

# 系统相关

## Win & Ubuntu 双系统

### Win 安装 Ubuntu

进入 Win 下控制面板中的电源选项关闭快速启动。

使用磁盘管理工具在 Win 下将磁盘压缩出一块可用空间，100 GB 左右。<mark>如果硬盘为动态磁盘，需先使用[傲梅分区助手](https://www.disktool.cn/)将磁盘转化为基本磁盘</mark>。

在 Ubuntu [官网](https://ubuntu.com/download/desktop)下载安装文件。按照官网[教程](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0)制作装机 U 盘。

U 盘插入电脑，重启进入 BIOS 选择从 U 盘启动。按照安装步骤进行，安装类型页面选择与 Win 共存。

安装完成后，在 `Software -> Additional Drivers` 里安装 Nvidia 显卡驱动，如果未关闭 Secure Boot，则需要设置密码并重启，在界面中选择 `Enroll Key` 并输入密码即可。

网上大多数教程还是以 Legacy 模式安装，关闭 Secure Boot，手动分区，用软件制作引导。而目前的版本完全不需要这么麻烦

## WOL

<mark>首先查看主板是否支持 WOL</mark>

开启 WOL 服务:

- Ubuntu：开启 BIOS 中 WOL (Wake on Lan)。安装 `sudo apt-get install ethtool` 并开启网卡的 WOL 服务 `sudo ethtool -s eth0 wol g`
- Mac：开启 `Energy Saver -> Power Adapter -> Wake for Wi-Fi network access`

发送 Magic Packet 以唤醒其它机器:

- 安装 `wakeonlan`，使用 `wakeonlan -i ip -p port macaddress`

# Tips

## SSH With Tmux

使用 `ssh NAME -t tmux new -A` 可以在 ssh 时自动 attach 到 tmux session，如果没有则自动创建

## History 增加时间显示

在 `.zshrc` 中添加 `HIST_STAMPS="yyyy-mm-dd"`

## Mac System Copy

`pbcopy` 只能够复制文本内容，如果达到系统复制的效果可以将下列函数加入 `.zshrc`

```bash
syscp() {osascript -e{'on run{a}','set the clipboard to posix file a',end} "$(readlink -f -- "$1")"}
```

通过 `syscp FILE` 来复制文件，注意只能复制单个文件
