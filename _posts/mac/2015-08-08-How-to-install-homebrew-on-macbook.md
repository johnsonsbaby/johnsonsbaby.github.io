---
category: linux
published: true
layout: post
title: 『 Mac 』在mac上安装Homebrew并换国内源的一次完整记录
description: Homebrew，mac上的软件管家，简单一条命令就可以实现包管理，而不用关心各种依赖，十分方便快捷。
---

## 使用背景

习惯了使用终端`Terminal`的开发者在Mac平台下难免会用到各种常用的软件，比如下载个QQ音乐、安装个开发环境、安装一些常用命令行工具等等；如果一个一个的去官网下载，就会显得无比麻烦。Homebrew这个Mac平台下的软件管理工具，简直就是懒人开发者的福音；下面就安装及卸载、换源和使用做一下相应的介绍。

## 安装和卸载

### 1、官网安装

Homebrew官网 [https://brew.sh/](https://brew.sh/)

```shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

遗憾的是，官网提供的脚本经常由于网络原因下载不下来，影响安装，所以本站提供了备份脚本.
[Homebrew_install](../data/mac/brew/install)
下载完成后执行`ruby -e install`

### 2、如何卸载

```shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

本站同样也提供了卸载脚本
[Homebrew_uninstall](../data/mac/brew/uninstall)
下载完成后执行`ruby -e uninstall`


### 3、更换国内源

安装脚本下载下来之后，安装过程中发现总是会被中断掉，原因是brew的代码库大，文件多，网络也慢；比较靠谱的做法是，修改`install`安装脚本中的brew的源码库为国内的镜像库地址，这里我推荐使用中科大(https://mirrors.ustc.edu.cn/)。

换源的操作也非常简单，找到`install`脚本中`BREW_REPO`关键字，然后将`=`后的链接改为`https://mirrors.ustc.edu.cn/brew.git`

```shell
BREW_REPO = "https://mirrors.ustc.edu.cn/brew.git".freeze
```

修改完成后再执行安装命令就可以了，安装完成后还需要设置一下`homebrew-core`和`homebrew-cask`两个仓库的地址，具体步骤如下：

```shell
$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
$ git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
$ git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

如果还没有那两个仓库的文件夹，则需要手工创建执行；至此，已经完成了大部分的换源设置，最后一步，也就是在运行brew前设置一下环境变量`HOMEBREW_BOTTLE_DOMAIN`，值为`https://mirrors.ustc.edu.cn/homebrew-bottles`。

`Homebrew Bottles`源是预编译二进制软件包，收录了仓库有以下几个
* homebrew/homebrew-core
* homebrew/homebrew-dupes
* homebrew/homebrew-php
* homebrew/homebrew-science
* homebrew/homebrew-nginx
* homebrew/homebrew-apache
* homebrew/homebrew-portable

环境变量的设置需要根据您当前的终端的情况来设置

如果您是`bash`用户：

```shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```

如果您是`zsh`用户：

```shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

好了，换源工作完成了。

## 如何使用

经历了以上安装和换源的一系列配置，终于到了使用阶段，来我们一起见识一下homebrew强大的能力吧。

首先，先更新一下，同步官方最新的仓库代码

```shell
$ brew update
```

我喜欢听歌，来安装个音乐播放软件吧，可是我不知道软件的具体名称，没关系，先试一下吧

```shell
$ brew search music
==> Formulae
game-music-emu                                          libmusicbrainz

==> Casks
amazon-music                                            musictube
background-music                                        neteasemusic
ieasemusic                                              qianqianmusic
kugoumusic                                              qqmusic
marshallofsound-google-play-music-player                yt-music
music-manager                                           homebrew/cask-versions/background-music-pre
musicbrainz-picard
```

果然，根据名称可以看到熟悉的QQ音乐、网易音乐、酷狗音乐等，选择喜欢的安装吧

```shell
$ brew install qqmusic
Error: No available formula with the name "qqmusic" 
Found a cask named "qqmusic" instead. Try
brew cask install qqmusic
```

报错了，根据提示再来一遍
```shell
$ brew cask install qqmusic
==> Downloading https://dldir1.qq.com/music/clntupate/mac/QQMusicMac_Mgr.dmg
######################################################################## 100.0%
==> No SHA-256 checksum defined for Cask 'qqmusic', skipping verification.
==> Installing Cask qqmusic
hdiutil: attach: WARNING: ignoring IDME options (obsolete)
==> Moving App 'QQMusic.app' to '/Applications/QQMusic.app'.
🍺  qqmusic was successfully installed!
```

还有一些其他常用的命令直接输入`brew`查看官方文档
```shell
$ brew 
Example usage:
brew search [TEXT|/REGEX/]
brew info [FORMULA...]
brew install FORMULA...
brew update
brew upgrade [FORMULA...]
brew uninstall FORMULA...
brew list [FORMULA...]

Troubleshooting:
brew config
brew doctor
brew install --verbose --debug FORMULA

Contributing:
brew create [URL [--no-fetch]]
brew edit [FORMULA...]

Further help:
brew commands
brew help [COMMAND]
man brew
https://docs.brew.sh
```
