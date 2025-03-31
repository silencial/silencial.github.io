---
title: CS Tools
date: 2020-03-27
lastmod: 2021-04-30
categories:
- Tech
tags:
- CS
- Tool
---

介绍 CS 中常用工具，参考 [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/) 课程。

其它相关 Blog:

- [常用软件](https://silencial.notion.site/cb9a9089382f4f76ba1d82ba3e50ec3f?v=15faca853b4a45d3a085a8238f6220b4): Notion 个人主页
- [Setup]({{< ref "setup" >}}): 自用工具及配置
- [Git 101]({{< ref "git" >}}): 常见的 git 命令
- [Regular Expression]({{< ref "regex" >}}): 常见的正则表达式用法
- [Linux]({{< ref "linux" >}}): 介绍 Linux 基本知识与操作

<!--more-->

---

# Shell

## Bash

[Bash 脚本教程](https://wangdoc.com/bash/)

[Minimal safe Bash script template](https://betterdev.blog/minimal-safe-bash-script-template/)

### 基本知识

- `shebang`: 写在脚本第一行，用来表示用什么执行。例如 `#!/usr/bin/env bash` 或 `#!/usr/bin/env python`。使用 `env` 会自动搜索 `PATH` 环境变量。
- 变量赋值 `foo=bar`。==注意 `=` 前后不能有空格==
- 字符串可由 `''` 或 `""` 表示，但 `""` 会替代变量，如 `echo "$foo"` 会输出 `bar`。==而单引号保持内部所有字符的本义==
- 脚本执行完会有返回值，0 表示正常
- 通配符
  - `?` 表示一个字符
  - `*` 表示任意个数字符
  - `[]` 匹配任意一个括号内的字符
  - `{}` 自动展开所有括号内的字符。例如 `{a,b}/c` 表示 `a/c b/c`
  - `..` 循环。例如 `1..3` 表示 `1,2,3`
- 执行比较时使用 `[[]]`。[比较详细命令](http://man7.org/linux/man-pages/man1/test.1.html)
- 多命令:

  - `CMD1; CMD2`: 顺序执行
  - `CMD1 && CMD2`: 当 1 被正确执行时才会执行 2
  - `CMD1 || CMD2`: 当 1 未被正确执行时才会执行 2
  - `CMD1 | CMD2`: 1 的正确输出作为 2 的操作对象
- 将命令返回作为变量

  - command substitution: `for file in $(ls)`
  - process substitution: `<(CMD)` 执行 `CMD` 并将输出保存至临时文件，再返回文件名

### 特殊字符

- `$0`: 脚本名
- `$1` to `$9`: 传递至脚本的变量，`$1` 代表第一个变量，以此类推
- `$@`: 所有的变量
- `$#`: 变量个数
- `$?`: 前一条命令的返回值
- `$$`: 当前脚本执行的进程 ID
- `!!`: 上一条命令。例如 `sudo !!`
- `$_`: 上一条命令的最后一个变量。在交互式 shell 中还可以通过 `Esc` + `.` 的方式实现

[完整列表](https://www.tldp.org/LDP/abs/html/special-chars.html)

### 快捷键

**移动**:

- ctrl+u: 清空当前行
- ctrl+a: 移动至行首
- ctrl+e: 移动至行尾
- alt+f: 按单词前移
- alt+b: 按单词后移
- ctrl+k: 删除光标到行尾的内容
- alt+backspace: 从光标删除至字首
- alt+d: 从光标删除至字尾
- ctrl+c: 终止命令

**任务控制**:

在 shell 中，窗口和会话是绑定的。

- ctrl+c: 向进程传递 `SIGINT` 信号，不一定停止
- ctrl+\\: 向进程传递 `SIGQUIT` 信号，终止进程
- ctrl+z: 向进程传递 `SIGSTOP` 信号，暂停进程
- 关闭命令行窗口等于向进程传递 `SIGHUP`，终止进程

## Zsh

- `cd` 可以省略
- 快速往上跳转 n 层目录: n+1 个 `.`
- 进程补全: `kill process_name` 并按 Tab 键自动替换进程 id。
- 使用 `d` 可以列出最近访问过的目录，然后输入对应数字进入目录。
- 如果要进入到 `~/workspace/src/dict` 目录，可以输入 `cd ~/w/s/d` 并按 Tab 进行补全。
- 快速重复上一条命令: `r`。
- 历史记录支持受限查找: 先输入 `git`，再按 ↑ 会搜索所有用过的 `git` 命令。
- 通配符搜索: `ls **/*.png` 搜索当前目录下所有 .png 文件。

## 常用命令

### 文件处理命令

`ls [选项] [文件或目录]`，`l='ls -lah'` 详细显示所有文件

`du -h -d 0 [目录]` 查看文件夹大小，`-d` 代表 depth

`mkdir -p [目录名]` 递归创建目录，`md='mkdir -p'`

`cd [目录]` 打开目录

`rm -rf [文件或目录]` 强制删除文件或目录。`-r` 参数为了递归删除目录

`cp [选项] [原文件或目录] [目标目录]` 拷贝

- `-r` 复制目录
- `-p` 连带文件属性复制
- `-d` 若源文件是链接文件，则复制链接属性
- `-a` 相当于 `-rpd`

`mv [原文件或目录] [目标目录]` 剪切或改名

`x=extract` 解压文件

### 查找

**查找文件**:

`locate [文件名]` 在后台数据库(默认一天更新一次)中按文件名搜索，速度更快。

- `updatedb` 强制更新数据库

- `/etc/updatedb.conf` 搜索条件配置文件

  ```bash
  PRUNE_BIND_MOUNTS="yes"  # 开启搜索限制
  PRUNEFS=  #　不搜索的文件系统
  PRUNENAMES=  # 不搜索的文件类型
  PRUNEPATHS=  # 不搜索的路径
  ```

`find [搜索范围] [搜索条件]` 使用通配符，属于完全匹配

- `-name` 搜索名字
- `-iname` 不区分大小写

```bash
# 查找修改时间超过十天的文件
find . -mtime +10

# 查找小于 25 KB 的文件
find . -size -25k

# 查找大于 50 KB 或小于 20 KB 的文件
find . -size +50k -o -size -20k

# 查找大于 20 KB 且小于 50 KB 的文件，并显示详细信息
find . -size +20k -size -50k -exec ls -lh {} \;
```

**查找命令**:

`whereis [命令名]` 搜索命令所在路径及帮助文档所在位置

- `-b` 只查找可执行文件
- `-m` 只查找帮助文件

`which [命令名]` 查找当前环境下调用的命令所在位置，可显示别名

**查找文件内容**:

`grep [选项] 字符串 文件名` 在文件当中匹配符合条件的字符串，使用正则表达式，属于包含匹配。也可以用来搜索命令返回值

- `-i` 忽略大小写
- `-v` 排除指定字符串
- `-C` 打印 context，例如 `grep -C 2` 会打印目标上下各 2 行。

### 任务

`kill [PID]`: 终止进程

`pkill [PIDNAME]` 按照名称终止进程，可使用 Tab 补全

`fg/bg`: 在前台/后台继续暂停的进程

`jobs`: 查看未完成的进程

### 其它命令

`man [命令]`: 命令帮助文档

`systemctl suspend`: 休眠

`cat [file] | xclip -selection clipboard`: 复制文件内容至系统剪贴板

`sips --resampleWidth []`：脚本图像处理，**MacOS 独有**

# SSH

Unix 系统中 ssh 分为 openssh-client 和 openssh-server。前者自带，允许连接其它机器。

开启 ssh server:

- Ubuntu: 安装 `sudo apt-get install openssh-server`
- Mac: 打开 `Sharing -> Remote Login`

查看 ssh 服务否打开: `ps -e | grep ssh`。如果出现 sshd 字样则已经启动

server 的配置文件在 `/etc/ssh/sshd_config` 中，可以修改端口等设置，默认端口为 22

使用 `ifconfig` 查看本机 ip 地址，其它机器使用 `ssh username@ip` 方式进行连接

配置 `~/.ssh/config` 文件，加入如下内容，之后可以使用 `ssh [Host]` 来进行连接

```txt
Host [自定义名称]
HostName [ip 地址]
User [server 用户名]
Port [端口号]
IdentityFile [私钥路径]
```

免密码登录:

```bash
# 生成密钥（公钥+私钥）
ssh-keygen -t rsa -C "comment"
# 将公钥传至 server
ssh-copy-id -i ~/.ssh/id_rsa.pub username@ip
```

外网连接:  使用路由外网 ip 地址并在路由上设置端口转发

**注意事项:**

- ip 地址分清是内网还是公网。例如连接在同一个路由器的设备都属于一个内网，由路由器分配的内网 ip 地址只允许在内网内进行连接
- 查询路由中自动分配的 ip 地址是否为外网，==有可能属于更大的运营商内网==
- 如果想通过外网连接，由于 ip 地址为动态，需要购买 DDNS (动态域名) 进行解析
- 美国这边 ip 基本不变

# Vim

![Vim Keyboard](https://i.imgur.com/KR5bTON.png)

使用 `vimtutor` 命令学习

## 操作模式

- Normal: 移动光标。`Esc` 切换
- Insert: 插入文本。`i` 切换
- Replace: 替换文本。`R` 切换
- Visual: 选择文本。`v` 切换
- Command-line: 执行命令。`:` 切换

同样的按键在不同的模式中可以实现不同的功能

## Vim 参数

- `vim + [file]` 定位到最后一行
- `vim +3 [file]` 定位到第三行
- `vim +/xxx [file]` 定位到 xxx 第一次出现的一行，`n` 进行切换
- `vim [file1] [file2] [file3]` 一次性打开多个文件，`:n` 切换下一个文件，`:N` 或 `:prev` 切换上一个文件，`:b [tab]` 自动补全切换文件
- `vim -p [file1] [file2] [file3]` 标签页打开多个文件，`gt/gT` 切换下/上一个标签

## 命令

- `:w` 保存
- `:q` 退出
- `:wq` 保存且退出
- `:x` 保存（仅在文件有修改时）且退出
- `:e [file]` 打开文档
- `:[command]!` 强制执行
- `:ls` 列出打开的所有文件
- `:xxx` 定位到第一次出现 xxx 的地方
- `?xxx` 向前搜索
- `:h [topic]` 打开帮助文档，可以是命令或按键
- `:w [name]` 当前文档另存为，若有 visual 选中则只另存为选中部分
- `:r [name]` 读取文件内容插入至光标下方，也可以读取命令 `:r !ls`

## 移动

- `h/j/k/l` 光标左/下/右/上移
- `w/b/e` 下一个单词/单词开头/单词结尾
- `0/^/$` 行首/行第一个非空字符/行尾
- `H/M/L` 屏幕顶部/中部/底部
- `C+b/f`  向上/下翻页
- `C+u/d`  向上/下翻半页
- `gg/G` 文件开头/结尾
- `:15` 到第 15 行
- `15G` 到第 15 行
- `/[regex]` 搜索，`n/N` 寻找下/上一个
- `f/F` 向后/前查找，光标移动至查找对象
- `t/T` 向后/前查找，光标移动至查找对象前一个位置
- `m[X]` 设定一个 mark，可以通过 ``[X]` 定位
- `ctrl+o/i` 回到前一个/后一个位置
- `%` 匹配括号

在 Visual 模式使用移动来进行选取

## 编辑

- `o/O` 在下/上一行插入
- `>` 缩进。`>>` 缩进本行
- `d[motion]` 删除。`dw` 删除单词，`d$` 删除至行尾，`d0` 删除至行首，`dd` 删除整行
- `c[motion]` 修改。`cw` 修改单词。等价于 `d[motion]` + `i`
- 尽量使用 text objects 替换 motion。`iw`: inner word; `it`: inner tag; `i"`: inner quotes; `ip`: inner paragraph; `as`: a sentence.
- `x` 删除字符。等价于 `dl`
- `s` 替换字符。等价于 `xi`
- `:s/[a]/[b]` 将光标所在行的第一个匹配 a 替换为 b。`:s/a/b/g` 替换该行所有匹配
- `:[#],[#]s/[a]/[b]/g` 将 `#,#` 之间的行进行匹配替换
- `:%s/[a]/[b]/g` 匹配替换整个文件
- `:%s/[a]/[b]/gc` 在每次替换时进行询问
- `r` 替换字符。区别在于不进入插入模式
- `R` 进入替换模式
- `yy` 复制一行，`yw` 复制单词
- `p/P` 在光标所在行下/上方粘贴
- `u/U/ctrl+r` 撤销/撤销整行/重做
- Visual 模式选取后 `d` 删除，`c` 修改

## 特殊

- `.` 重复上次操作
- 搜索可以搭配 `c/d` 使用，如 `dt"` 删除该行在第一个 " 前的内容
- 数字+字母可以进行重复操作。例如 `3w` 向前移动三个单词
- 删除文本会自动放入 register 中
- `ctrl+g` 显示当前文件路径和状态
- 在命令前加 `no` 关闭选项，例如 `:set noic`
- 命令模式下 `ctrl+d` 会显示补全选项
- `:so %` 表示加载当前文件为配置文件，`:so ~/.vimrc` 指定文件

# Tmux

**会话**: 每个会话都是一个独立的工作空间，可以有多个窗口

- `tmux` 开启新会话
- `tmux new -s [NAME]` 开启新会话并命名
- `tmux new -A` 接入最后一个会话，如果没有则新建
- `tmux ls` 显示当前的所有会话
- `<C-b> d` 分离当前会话（退出窗口，进程在后台运行）
- `tmux a` 接入最后一个会话
- `tmux a -t [name]` 接入特定名称的会话

**窗口**: 类似编辑器的标签页，同属于一个会话。

- `<C-b> c` 新建窗口
- `<C-d>` 关闭窗口
- `<C-b> [N]` 跳到第 N 个窗口
- `<C-b> p/n` 跳到前/后一个
- `<C-b> ,` 重命名当前窗口
- `<C-b> w` 列出当前所有窗口

**窗格**: 对一个窗口进行分栏

- `<C-b> "` 水平划分窗格
- `<C-b> %` 垂直划分窗格
- `<C-b> [方向键]` 切换到方向键对应的其它窗格
- `<C-b> z` 全屏/取消当前窗格
- `<C-b> <space>` 改变窗格排列

# Git

参见这篇 [Blog]({{< ref "git" >}})

# Debugging & Profiling

## Debugging

`ipdb`: 用来 debug Python，为 `IPython` 版本的 `pdb`，支持补全，高亮等功能。

`python -m ipdb [文件]`

- **l**(ist): 显示当前行周围的 11 行代码，可以重复使用
- **s**(tep): 执行当前行
- **c**(ontinue): 执行到断点或出错的位置
- **b**(reak): 设置断点
- **p**(rint): 打印当前环境下变量
- **r**(eturn): 继续执行直到当前函数返回
- **q**(uit): 退出

## Profiling

`time`: 计算命令耗时

- real: 实际花费总时间
- user: 在用户空间花费的 CPU 时间
- sys: 在内核空间花费的 CPU 时间

python 中可以使用 [line_profiler](https://github.com/rkern/line_profiler) 模块对每一行计时，[memory_profiler](https://pypi.org/project/memory-profiler/) 进行内存计算

# Make

Make 是最常用的构建系统。任何只要文件变化就要重新构建的项目都可以用 Make 构建。

本质上，Make 只是一个根据指定的 Shell 命令进行构建的工具，规则通过 Makefile 编写。

## Makefile

Makefile 范例：（注意第二行需要以 tab 键开头）

```makefile
[target]: [dependency1] [dependency2] ...
    [command]
```

- `dependency` 也可以有自己的构建规则，并组成 `dependency graph`
- `target` 除了可以是文件之外也可以是命令，称为 `phony target`。为了避免与将当前目录下的同名文件混淆，需要在开头声明 `.PHONY` 变量

```makefile
.PHONY clean all

all: hello.out  # PHONY target

hello.out: hello.c min.o max.o
    gcc -o hello.out hello.c min.o max.o
# 若存在依赖文件无法找到，继续向后搜索该文件的依赖性，如此递进下去
min.o: min.c
    gcc -c -o min.o min.c
max.o: max.c
    gcc -c -o max.o max.c

clean:  # PHONY target
    rm *.out *.o
```

## 语法

使用变量：

```makefile
txt=HELLO
test:
    echo $(txt)
```

关闭打印：`make` 默认执行时会打印每条语句

```makefile
test:
    @# test
```

自动变量：

- `$@`: 表示 target
- `$^`: 表示所有 dependency
- `$<`: 表示第一个 dependency

```makefile
a.txt: b.txt
    echo $^ > $@
```

模式匹配：

```makefile
.PHONY all

all: a.csv b.csv c.csv

%.csv: %.txt  # 匹配所有 a/b/c.csv，同时 dependency 保持和 target 相同的匹配
    echo $^ > $@
```

内置函数：

- `wilcard`: 通配符匹配
- `patsubst [pattern],[replacement],[text]`: 模式匹配替换

```makefile
file = $(wildcard src/*.txt)
$(patsubst %.txt,$.csv,a.txt,b.txt)
```

## Make 命令

运行 `make` 时，会在当前目录下寻找 Makefile，只有当 `target` 不存在或比 `dependency` 旧时才会执行 `command`

- `make [target]`: 指定 make 目标，若没有则默认执行第一个
- `make -n [target]`: dry-run
- `make -B [target]`: 无条件构建目标
- `make -C [dir] [target]`: 改变 make 的运行目录
- `make -f [file] [target]`: 指定 makefile 文件

# C++

## GCC

GCC (GNU Compiler Collection) 是 C 的编译器，编译 C 源码有四个步骤：预处理-----> 编译 ----> 汇编 ----> 链接

1. 预处理阶段，编译器将 C 源代码中的包含的头文件编译进来。

   `gcc -E hello.c -o hello.i`

2. 编译阶段，gcc 检查代码的规范性、是否有语法错误等，以确定代码的实际要做的工作。检查无误后，把代码翻译成汇编语言。

   `gcc -S hello.i -o hello.s`

3. 汇编阶段，把编译阶段生成的 .s 文件转成二进制目标代码。

4. 链接阶段，生成可执行文件

- `-E` 仅执行编译处理
- `-S` 将 c 代码转换为汇编代码
- `-c` 仅执行编译操作，不进行连接操作
- `-o` 指定生成的输出文件
- `-wall` 显示警告信息

## CMakeLists.txt

CMake 是 Makefile 的上层工具，目的为了自动产生可移植的 Makefile。CMake 的配置文件：

```cmake
# cmake 最低版本需求
cmake_minimum_required(VERSION 3.19)

# 工程名称
project(Test)

# 设置编译模式 Debug/Release
set(CMAKE_BUILD_TYPE "Debug")

# 添加可执行程序
add_executable(a a.cpp)

# 添加库
add_library(b b.cpp)  # 静态库
add_library(b_ SHARED b.cpp)  # 共享库

# 添加可执行程序并链接至库
add_executable(c c.cpp)
target_link_libraries(c b)
```

- 将 `a.cpp` 编译为可执行文件（执行 `main` 函数）
- 将 `b.cpp` 编译为库文件，静态或共享
- `c.cpp` 中调用了 `b.cpp` 的函数，因此需要链接到 `b` 的库文件。同时需要定义一个 `b.h` 的头文件声明 b 中的函数，并在 `c.cpp` 中使用 `include "b.h"`

## 编译及安装

```bash
cd <project>
mkdir build && cd build
cmake ..  # 在上一级文件夹中按照 CMakeLists.txt 生成 Makefile 至当前文件夹
make  # 编译
make install  # 安装
```

通过生成 `build` 文件夹，将源码和生成文件分开。`make install` 需要 `CMakeLists.txt` 中定义  `install` 命令。

一般第三方库文件的 `install` 命令会将头文件、库文件、可执行文件分别安装至 `CMAKE_INSTALL_PREFIX` 变量（默认为 `/usr/local`）下的 `include/`, `lib/` 和 `bin/` 文件夹中。之后即可以在 `CMakeLists.txt` 中使用 `find_package` 语句方便调用。

卸载时使用`make uninstall`，但作者可能没有定义 `uninstall` 命令，此时可在 `make install` 执行的文件夹中找到 `install_manifest.txt` 文件，列出了所有安装文件的路径。使用 `xargs rm < install_manifest.txt` 删除即可。

可以通过 `cmake -DCMAKE_INSTALL_PREFIX=<path>` 来改变安装位置，方便管理。

### CMake Package Registry

当 CMake 只通过 `make` 而没有 `install` 时依旧能通过 `find_package` 找到包时，说明原先的 `CMakeLists.txt` 中存在 `export` 语句，通过 User Package Registry 的方式指定了路径。文件位置在 `~/.cmake/package/<package>` ，其中的文件可以为任意名称，内容是搜索路径。

## find_package

导入第三方库。使用方法为

```cmake
find_package(<package> REQUIRED)
```

该语句主要会在以下变量定义的地址寻找 `<package>Config.cmake` 的文件并导入，或通过上面所说的 Cmake Package Registry 的方式。完整寻找方式参照 [Search Procedure](https://cmake.org/cmake/help/latest/command/find_package.html#search-procedure)

```txt
<PackageName>_DIR
CMAKE_PREFIX_PATH
PATH
```

一般来说使用 `find_package` 语句后会得一些关于该包的变量，以便之后进行调用

```cmake
<package>_INCLUDE_DIRS
<package>_LIBS
```

# Q & A

## source & exec & ./script

- `source` 会在当前 shell 中执行命令
- `exec` 会终止当前 shell，并重新生成一个新的进程来执行命令
- `./script` 生成一个新的 shell 环境，在该 shell 中执行命令后退出

应使用 `exec zsh` 而非 `source ~/.zshrc` 来重新加载 `.zshrc` 文件

## Mac 上某些命令和 Linux 不同

Linux 使用的命令为 GNU 版本，Mac 上可以使用 `brew install coreutils` 来进行安装，安装后的命令可以在 `/usr/local/bin` 中找到，以 `g` 作为前缀，若想替代 Mac 本身的命令则直接在该目录下建立软链接即可。（默认 `PATH` 中 `/usr/local/bin` 的优先级高于 `/usr/bin`）

## 文件夹/文件命令规则

**不要不要不要**加空格，在命令行中会造成很多麻烦。如果无法避免且只是少数文件/文件夹的情况，可以在同一位置建立良好命名的软链接。

# Trivia

- `.zshrc` 中的 `rc` 是 run command 的意思
