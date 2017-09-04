title: MacBook——如何快速配置 iOS 开发环境
date: 2017-09-4 18:05:13
tags: [iOS,Swift,Mac]
---

## 前言
 &emsp;&emsp;作为一名开发者，我相信你对开发环境的配置并不陌生，无论你是 J2EE、 Android 还是 iOS 开发者，拿到新电脑后第一件事情也就是去配置开发环境。如果你是一名开发老司机，你可能会熟练的配置好开发环境并迅速的就能进行开发，但对于 freshman 来说这个过程就有点漫长或者说是相当痛苦的。我们都是比较懒的，这么繁杂的配置，我们为什么不可以用一个脚本让他自动执行呢？
![start work](http://upload-images.jianshu.io/upload_images/121208-bbe2ff6a4bc28e5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/550)

<!-- more -->

## 正文

  &emsp;&emsp;作为Mac 上有很多激动人心的工具，我们会时常去收集，并在不断的尝试去更新自己的开发工具库。如果你不是独立开发者，那么也就意味着你有一个团队，为了协同工作，我们通常也会在团队中形成一套统一的开发工具集。因此我们可以在 git 上创建一个工程去管理我们的开发环境。那么就让我们简单的来完成一个这样的项目吧。做为 iOS 开发者，去 App store 下载并安装 Xcode，这一步不可避免👻，紧接着，安装 Xcode command line tools，运行：

```shell
xcode-select --install
```
这一步将完成  Xcode command line tools 的安装。本工程主要通过 fabric + shell 实现脚本自动化，所以我们还需安装 fabic 环境:

```shell
sudo easy_install -U pip
sudo pip install Fabric -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```
根据这行命令的的指引，我们将成功安装 Fabric。看到这里，也许你会问，Fabric 是嘛玩意，对我们有什么帮助？

#### [Fabric](http://www.fabfile.org/index.html)
   &emsp;&emsp;作为Fabirc 是基于 python 实现的 SSH 命令行工具, 在自动化部署领域有着非常广泛的应用。
  那么怎么使用呢？在默认情况下使用 Fabric 我们需要在跟目录下创建 fabfile.py 入口文件，然后我们就可以在这个文件里进行脚本编写了。在文件开头我们首先导入我们所需要使用的 Fabric 库：

```python
import os
import sys

from fabric.colors import blue, cyan, green, magenta, red, yellow
from fabric.decorators import task
from fabric.operations import local
from fabric.state import env
from fabric.utils import puts
```
这里我们队 Fabric 导入的接口进行简要的说明：

| api |   说明
|---|---
|colors|需要的颜色选定
| local |配合 Shell 执行本地命令
|env| fabfile 的全局参数定义
|puts| 打印相当于 Swift 中的 print 函数
|task| 被 @task 修饰的函数可以用 fab command 调用

了解 Fabric 后我们来做一个 help 命令：

```python
# ============
# =   Help   =
# ============
@task(default=True, alias='help')
def help():
    """ look up the help doc """
    puts('*' * 60)
    puts('*  ' + cyan('  Fabric helps  '.center(58, '=')) )
    puts('*' + ' ' * 58 )
    puts('*' + green('  lookup all the Commands: fab -l'.ljust(64)))
    puts('*' + green('  lookup the detail of a specific command: fab -d command'.ljust(64)) )
    puts('*' + yellow('  execute a command with para: fab command: para'.ljust(70)))
    puts('*' + ' ' * 58 )
    puts('*' * 60)
```
接下来我们来看看这个 help 命令的运行效果：

![command-help](http://upload-images.jianshu.io/upload_images/121208-db7ad4635367c85d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/550)
这是一个好的开始。

#### softwares

  &emsp;&emsp;作为我们知道  fabric + shell 的简单使用可以实现一个自动化脚本，那么我们到底要用这个脚本安装哪些 software 呢？其实这个是因人而异的，这里我简单罗列一下我所使用的一些工具，您如果有更好的工具，请推荐给我：

1. [Homebrew](https://brew.sh/index_zh-cn.html)
  &emsp;&emsp;作为主流的  Linux 都有包管理器， Red hat 有 yum，Ubuntu 有 apt-get，但不好意思，Mac OS 没有这玩意，[Homebrew](https://brew.sh/) —— Mac OS 缺失的软件包管理器，那么它 能干什么呢? 当然是管理 Mac OS 上的软件包。比如说我想安装 wget ：

```
$ brew install wget
```
想卸载：

```
$ brew uninstall wget
```
我们可以看到有了 Homebrew ，我们可以轻松的管理这些软件包。

2. [Oh My ZSH](http://ohmyz.sh/)
&emsp;&emsp;其实 OS X 默认的 bash 已经非常强大了，但是你绝不会拒绝更优秀的 ZSH 。关于为什么使用 ZSH ，请移步 [使用zsh的十大优点](http://shanker.blog.51cto.com/1189689/1765036) 。由于 ZSH 的配置实在太复杂了，于是就出现了 [Oh My ZSH](https://github.com/robbyrussell/oh-my-zsh) , 有了它，我们可以轻松的配置我们的 zsh 了：

```shell
    curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```
3. [CocoaPods](https://cocoapods.org/)
&emsp;&emsp; CocoaPods ，对于大家来说，我想并不陌生，它经常能帮助你快速的管理你的三方开源库，包括 `install`、`update` 等操作。 因此 CocoaPods 也是我们开发中必不可少的一个工具：

```shell
sudo gem install cocoapods
```
......
常用的软件，我想我就不一一列举了，大家都有自己的习惯，和常用的软件。

#### 脚本化
&emsp;&emsp;我们定义一个 ` install`  函数，并将我们所要安装的软件通过脚本来顺序安装 ，并用 `@task` 标记：

```python
    """install the group of software, example: fab install:ios"""

    if not os.path.exists('/usr/local/bin/brew'):
        puts(green('install Homebrew'))
        local('ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"')
    puts(green('install  proxy'))
    local('brew install proxychains-ng')
    local('sed -i "" "s/socks4[[:space:]][[:space:]]127.0.0.1[[:space:]]9050/socks5  127.0.0.1 1086/g" /usr/local/etc/proxychains.conf')
    local('brew install bash-completion ruby tree')
    local('brew link --overwrite ruby')
    puts(green('config RubyGems'))
    local('gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/')
    local('gem sources -l')
    puts(green('install BearyChat, GitHub Desktop, Google Chrome, ShadowsocksX-NG'))
    local('brew cask install bearychat github-desktop google-chrome shadowsocksx-ng')
    puts(green('install Atom, Charles, Dash'))
    local('brew cask install atom charles dash')
    puts(green('install CocoaPods'))
    local('sudo gem install cocoapods')
    puts(green('install Carthage, SwiftLint'))
    local('brew install carthage swiftlint')
    puts(green('install fastlane'))
    local('sudo gem install fastlane -NV')
    local('brew cleanup')
    local('brew cask cleanup')
    local('sudo gem clean')
```
如上代码，会逐行执行，等这段脚本执行完，这些软件也就安装好了。
同理，我们还可以定义一个用于软件更新的函数 `update` ：

```python
@task
def update(proxy=True, pypi_option=env.pypi_option):
    """update the tools to the latest version"""
    puts(green('update Homebrew'))
    local('brew upgrade')
    local('brew cleanup')
    puts(green('update pip, Pylint, Transifex Command-Line Tool, virtualenvwrapper, twine, Fabric'))  # https://github.com/Homebrew/legacy-homebrew/issues/25752
    try:
        local('sudo -H pip3 install -U pip pylint transifex-client twine virtualenvwrapper{}'.format(pypi_option))
    except Exception:
        pass
    local('sudo -H pip2 install -U pip{}'.format(pypi_option))
    local('sudo -H pip install -U --user Fabric{}'.format(pypi_option))  # https://github.com/pypa/pip/issues/3165
    puts(green('update RubyGems'))
    local('sudo gem update')
    local('sudo gem clean')
```
这样，我们就可以通过脚本定期更新我们的软件了。

水平有限，如有疏漏，望指正
欢迎交流，欢迎 star  [CNKCQ](https://github.com/CNKCQ)
遗人🌹，手有余香      [blog](http://wangchenquan.com/)
