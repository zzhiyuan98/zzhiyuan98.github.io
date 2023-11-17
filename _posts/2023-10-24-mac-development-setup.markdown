---
layout: post
title: Mac 前端开发环境配置指南
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
#### [安装 Git](https://git-scm.com/download/mac)

```shell
brew install git
```


#### 一台设备上管理多个 Git 服务器账号

参考：[Manage GitHub, and Gitlab accounts on single machine with SSH keys on Mac](https://medium.com/@viviennediegoencarnacion/manage-github-and-gitlab-accounts-on-single-machine-with-ssh-keys-on-mac-43fda49b7c8d)

配置单个 SSH key 可以参考 GitHub 的 [SSH 密钥指南](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)，这里就不赘述了，下面是为 GitHub 和 Gitlab 配置 SSH key 的步骤：

##### 1. 建立新的 SSH 密钥对

```shell
ssh-keygen -t ed25519 -C "personal_email@example.com" -f ~/.ssh/id_ed25519_github
ssh-keygen -t ed25519 -C "work_email@example.com" -f ~/.ssh/id_ed25519_gitlab
```

注：可以使用同一个邮箱，也可以将工作邮箱和个人邮箱做区分，见下方[配置 Git](#5-配置-git)

查看创建好的密钥对👇，id_ed25519_xxx 对应私钥，id_ed25519_xxx.pub 对应公钥

```shell
ls ~/.ssh
```

##### 2. 在代码托管平台上添加 SSH key，下面以 GitHub 举例

复制公钥到剪贴板

```shell
pbcopy < ~/.ssh/id_ed25519_github.pub
```

登录 GitHub -> Settings -> SSH and GPG keys -> New SSH key

在 Key 一栏粘贴公钥 -> 填写 Title -> Add SSH key

GitHub 的这一步就算完成了，Gitlab 也是同理

##### 3. 使用 ssh-agent 管理密钥

```shell
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_github
ssh-add ~/.ssh/id_ed25519_gitlab
```

##### 4. 修改 SSH 配置文件

```shell
vim ~/.ssh/config
```

```
Host github.com
  HostName github.com
  User personal_email@example.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519_github
  
Host gitlab.com
  HostName gitlab.com
  User work_email@example.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519_gitlab
```

##### 5. 配置 Git

如果两个平台使用的是同一个邮箱，修改全局配置就可以了

方法一

```shell
git config --global user.name "Julia Zhang"
git config --global user.email "work_email@example.com"
```

方法二

```shell
vim ~/.gitconfig
```

```
[user]
    name = Julia Zhang
    email = work_email@example.com
    signingkey = XXXXXXXX
```

没有配置 GPG key 的话可以不用写 signing key

如果使用的是不同的邮箱，和上面一样，先将使用最多的邮箱添加到全局配置里（这里假设是 work_email@example.com）

再在单个 repo 下修改配置，设置为 personal_email@example.com

方法一

```shell
cd local_repo_directory
git config --local user.name "Julia Zhang"
git config --local user.email "personal_email@example.com"
```

方法二

```shell
vim .git/config
```

```
[user]
    name = Julia Zhang
    email = personal_email@example.com
```

#### 配置 git alias
TBC
