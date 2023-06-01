---
layout: post
title: Jekyll 安装指南 Windows
---
大约一年前，我决定搭一个个人技术博客，当时基本是按照 [GitHub Pages Documentation] 写的一套流程来的

在安装 Jekyll 的时候虽然遇到了一些磕磕绊绊，但总体还算顺利（Mac 环境）

今年我打算重拾这个博客的时候，常用开发环境已经变成了 Windows

然后我就发现了非常致命的问题：Jekyll 官方给出的[安装指南]在 Windows 10 上是跑不通的，以下是我走过的路

1. `ruby -v`，`gem -v`，`gcc -v`，`g++ -v`，`make -v` 这几个命令都能正常运行
2. 在 [RubyInstaller] 下载安装 Ruby+Devkit，安装选项默认，最后一步的时候让程序运行 `ridk install` 并选择 `MSYS2 and MINIGW development tool chain`
3. 新起一个命令行窗口，执行 `gem install jekyll bundler`

第三步过程中出现报错 "Error: Not an ELF file"，在 Stack Overflow 上遍寻无果 

回到 Jekyll 官方指南，发现了如下提示👇

![warning](/assets/jekyll/warning.png)

于是我起了一个 Ubuntu 环境 ([Ubuntu on WSL])

按照官方的指示，使用 [Brightbox] 安装指定版本的 Ruby
```shell
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:brightbox/ruby-ng
$ sudo apt-get update
$ sudo apt-get install ruby2.5 ruby2.5-dev build-essential dh-autoreconf
```
添加 PPA 的时候遇到了 "user or team does not exist" 的报错，重装 CA 证书后问题得到了解决
```shell
$ sudo apt-get install --reinstall ca-certificates
```
下面是 Jekyll 官方给出的 next steps，执行起来也是一地鸡毛
```shell
$ gem update
$ gem install jekyll bundler
```
各类报错总结如下：
1. 在运行 gem install 时需要给出版本，比如安装的是 Ruby2.5，那么就要运行 `gem2.5 install bundler`
2. 会出现某个包强制要求 Ruby version >= 2.6.0，而官方建议安装的是 2.5.x
3. 安装了 Ruby 2.6 & Ruby 2.7 并配套安装 ruby-switch 切换版本后，以下三类命令都无法执行
```shell
$ gem install XXX
$ gem2.6 install XXX
$ gem2.7 install XXX
```
经历了这一系列的折腾之后，我突然觉得，markdown 嘛，又不是前端切图，不起本地服务也挺美的。。。

[GitHub Pages Documentation]: https://docs.github.com/en/pages/quickstart
[安装指南]: https://jekyllrb.com/docs/installation/windows/
[RubyInstaller]: https://rubyinstaller.org/
[Ubuntu on WSL]: https://ubuntu.com/tutorials/install-ubuntu-on-wsl2-on-windows-10#1-overview
[Brightbox]: https://www.brightbox.com/docs/ruby/ubuntu/
