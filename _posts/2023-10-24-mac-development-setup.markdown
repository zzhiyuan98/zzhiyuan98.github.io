---
layout: post
title: Mac 开发环境配置指南
---

### [Homebrew](https://brew.sh/)
非常好用的包管理工具，轻松实现安装、卸载、更新

安装命令👇

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### [iTerm2](https://iterm2.com/) + [oh-my-posh](https://ohmyposh.dev/)

iTerm2 是 Mac 默认 Terminal 的优质替代品，[点击这里](https://iterm2.com/downloads.html)下载安装

oh-my-posh 是一个终端美化工具，安装命令👇

```shell
brew install jandedobbeleer/oh-my-posh/oh-my-posh
```

官网上有好多预设的[主题](https://ohmyposh.dev/docs/themes)，可以在里面挑一个喜欢的进一步 customize

这里以 [atomic](https://github.com/JanDeDobbeleer/oh-my-posh/blob/main/themes/atomic.omp.json) 作为示例

![atomic](/assets/mac-setup/atomic.png)

执行以下命令可以预览效果，需要替换具体的 JSON 文件名，即 atomic.omp.json -> XXX.omp.json

```shell
eval "$(oh-my-posh init zsh --config $(brew --prefix oh-my-posh)/themes/atomic.omp.json)"
```

在没有安装特殊字体的情况下，出来的效果可能很丑，这是因为 Oh My Posh 需要配合 [Nerd Fonts](https://www.nerdfonts.com/) 使用，否则很多 icon 将无法正常显示

使用 Oh My Posh 提供的 CLI 安装 FiraCode

```shell
oh-my-posh font install
```

再在 iTerm > Settings > Profiles > Text > Font 里选择安装好的字体，就可以正常显示主题了

![font](/assets/mac-setup/font.png)

这个效果是即时的，如果我们新开一个命令行窗口，刚才的主题就消失了

想要长久保留主题效果的话，需要修改一下 .zshrc（或 .bashrc，如果使用的是 Bash）

```shell
vim ~/.zshrc
```

按 i 键入，把刚才的命令粘贴过去

```shell
eval "$(oh-my-posh init zsh --config $(brew --prefix oh-my-posh)/themes/atomic.omp.json)"
```

按 ESC，按 ZZ 保存退出

最后再执行一下这个文件，配置就生效了

```shell
source ~/.zshrc
```

> .zshrc 文件可以理解为每次打开 Zsh 都会执行一遍的脚本

iTerm 也有一些预设的主题色，在 Settings > Profiles > Colors > Color Presets 里 [Visit Online Gallery](https://iterm2colorschemes.com/)

![color](/assets/mac-setup/iterm-color.png)

以我使用的 [nord](https://raw.githubusercontent.com/mbadolato/iTerm2-Color-Schemes/master/schemes/nord.itermcolors) 配色举例，链接点开后右键保存为 nord.itermcolors 文件，再在之前的下拉框里选择就可以了 

下面是一些个人向的设定

![background](/assets/mac-setup/background.png)

最终的效果图

![effect](/assets/mac-setup/effect.png)

### Git
TBC
