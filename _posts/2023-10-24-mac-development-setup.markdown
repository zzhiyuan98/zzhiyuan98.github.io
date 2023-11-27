---
layout: post
title: Mac 前端开发环境配置指南
---

## [Homebrew](https://brew.sh/)
非常好用的包管理工具，轻松实现安装、卸载、更新

安装命令👇

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## [iTerm2](https://iterm2.com/) + [oh-my-posh](https://ohmyposh.dev/)

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

## Git
### [安装 Git](https://git-scm.com/download/mac)

```shell
brew install git
```


### 一台设备上管理多个 Git 服务器账号

参考：[Manage GitHub, and Gitlab accounts on single machine with SSH keys on Mac](https://medium.com/@viviennediegoencarnacion/manage-github-and-gitlab-accounts-on-single-machine-with-ssh-keys-on-mac-43fda49b7c8d)

配置单个 SSH key 可以参考 GitHub 的 [SSH 密钥指南](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)，这里就不赘述了，下面是为 GitHub 和 Gitlab 配置 SSH key 的步骤：

#### 1. 建立新的 SSH 密钥对

```shell
ssh-keygen -t ed25519 -C "personal_email@example.com" -f ~/.ssh/id_ed25519_github
ssh-keygen -t ed25519 -C "work_email@example.com" -f ~/.ssh/id_ed25519_gitlab
```

注：可以使用同一个邮箱，也可以将工作邮箱和个人邮箱做区分，见下方[配置 Git](#5-配置-git)

查看创建好的密钥对👇，id_ed25519_xxx 对应私钥，id_ed25519_xxx.pub 对应公钥

```shell
ls ~/.ssh
```

#### 2. 在代码托管平台上添加 SSH key，下面以 GitHub 举例

复制公钥到剪贴板

```shell
pbcopy < ~/.ssh/id_ed25519_github.pub
```

登录 GitHub -> Settings -> SSH and GPG keys -> New SSH key

在 Key 一栏粘贴公钥 -> 填写 Title -> Add SSH key

GitHub 的这一步就算完成了，Gitlab 也是同理

#### 3. 使用 ssh-agent 管理密钥

```shell
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_github
ssh-add ~/.ssh/id_ed25519_gitlab
```

#### 4. 修改 SSH 配置文件

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

#### 5. 配置 Git

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

### 配置 Git Alias
如果习惯在终端操作，可以为常用的 Git 命令添加 alias，例如：

```shell
git config global alias.co checkout
```

这样当我们想打 `git chekout master` 时，只需要打 `git co master` 就可以了

另一种方法是修改配置文件，可以批量添加 alias

```shell
vim ~/.gitconfig
```

```
[alias]
  co = checkout
  cb = checkout -b
  b = branch
  d = diff
```

如果想实现 `gco = git checkout` 的效果，可以在 .zshrc 里定义 alias

```shell
vim ~/.zshrc
```

```shell
# git aliases
alias gca="git commit --all -S"
alias gcb="git checkout -b"
alias gco="git checkout"
alias gd="git diff"
alias gpd="git push o HEAD -f"
alias pull="git pull --rebase"
alias grbi="git rebase -i"
alias grh="git reset --hard"
alias gdbr="git branch --list | grep -Ev '^\* ' | fzf -m | xargs -I {} git branch -D {}"
```

## [nvm](https://github.com/nvm-sh/nvm)
nvm (Node Version Manager) 是一个用来管理 node 版本的工具

在开发多个项目时，不同项目使用的可能是不同版本的 node，使用低版本的 node 去跑高版本的 node 项目可能会遇到报错，反过来也是一样

nvm 可以让我们安装不同版本的 node，并在不同的项目之间进行一个丝滑的切换

### [安装](https://github.com/nvm-sh/nvm#installing-and-updating)

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
```

### 使用
安装最新版本的 node

```shell
nvm install node
```

安装指定版本的 node

```shell
nvm install 14.1.0
```

切换到某个版本

```shell
nvm use 14.1.0
```

如果在单个项目的根目录下创建 .nvmrc 文件并写上指定的 node 版本（如 14.1.0），那么跑 `nvm use`、`nvm install`、`nvm which` 这些命令时就可以不用再指定版本了

```shell
echo "14.1.0" > .nvmrc
```

### [shell 集成](https://github.com/nvm-sh/nvm#deeper-shell-integration)
#### zsh
懒人福音，在有 .nvmrc 的目录下自动执行 `nvm use`

```shell
vim ~/.zshrc
```

```
# place this after nvm initialization!
autoload -U add-zsh-hook

load-nvmrc() {
  local nvmrc_path
  nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version
    nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$(nvm version)" ]; then
      nvm use
    fi
  elif [ -n "$(PWD=$OLDPWD nvm_find_nvmrc)" ] && [ "$(nvm version)" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}

add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

#### bash

```shell
vim ~/.bashrc
```

```
cdnvm() {
    command cd "$@" || return $?
    nvm_path=$(nvm_find_up .nvmrc | tr -d '\n')

    # If there are no .nvmrc file, use the default nvm version
    if [[ ! $nvm_path = *[^[:space:]]* ]]; then

        declare default_version;
        default_version=$(nvm version default);

        # If there is no default version, set it to `node`
        # This will use the latest version on your machine
        if [[ $default_version == "N/A" ]]; then
            nvm alias default node;
            default_version=$(nvm version default);
        fi

        # If the current version is not the default version, set it to use the default version
        if [[ $(nvm current) != "$default_version" ]]; then
            nvm use default;
        fi

    elif [[ -s $nvm_path/.nvmrc && -r $nvm_path/.nvmrc ]]; then
        declare nvm_version
        nvm_version=$(<"$nvm_path"/.nvmrc)

        declare locally_resolved_nvm_version
        # `nvm ls` will check all locally-available versions
        # If there are multiple matching versions, take the latest one
        # Remove the `->` and `*` characters and spaces
        # `locally_resolved_nvm_version` will be `N/A` if no local versions are found
        locally_resolved_nvm_version=$(nvm ls --no-colors "$nvm_version" | tail -1 | tr -d '\->*' | tr -d '[:space:]')

        # If it is not already installed, install it
        # `nvm install` will implicitly use the newly-installed version
        if [[ "$locally_resolved_nvm_version" == "N/A" ]]; then
            nvm install "$nvm_version";
        elif [[ $(nvm current) != "$locally_resolved_nvm_version" ]]; then
            nvm use "$nvm_version";
        fi
    fi
}

alias cd='cdnvm'
cdnvm "$PWD" || exit
```

## [WebStorm](https://www.jetbrains.com/webstorm/)
工作之前用的是 [VS Code](https://code.visualstudio.com/)，工作之后受前辈影响换了 WebStorm

使用 VS Code 的小伙伴后面就可以不用看了，等我以后换了 IDE 再来更新吧

推荐使用 [JetBrains Toolbox](https://www.jetbrains.com/toolbox-app/) 管理全家桶，轻松安装卸载升级

![toolbox](/assets/mac-setup/toolbox.png)

大家选择自己喜欢的 IDE 就好，这里简单罗列一下我的 IDE 配置（因为之前写过 IntelliJ IDEA 的配置，有些图我就用之前的了）

### 外观

推荐使用 JetBrains 的新 UI，界面非常 neat and clean

![new_ui](/assets/java-setup/new_ui.png)

暗色模式，字号看着调整

![theme](/assets/java-setup/theme.png)

换上程序员专属字体 [FiraCode](https://github.com/tonsky/FiraCode)

[安装](https://github.com/tonsky/FiraCode/wiki/Installing)

```shell
brew install font-fira-code
```

![ide-font](/assets/mac-setup/ide-font.png)

### 插件

![plugins](/assets/java-setup/plugins.png)

- IdeaVim：vim 插件
- GitToolBox：用来 git blame 的工具，会在代码的每一行后面显示 commit 的作者、时间和 commit message

![git_toolbox](/assets/java-setup/git_toolbox.png)

- IKun Progress：可以让你的进度条变成 ikun 打篮球

![ikun](/assets/java-setup/ikun.jpg)

- WakaTime：记录你的写码时间

![wakatime](/assets/java-setup/wakatime.png)

一览（要素拉满）

![preview.png](/assets/java-setup/preview.png)

### [终端打开 IDE](https://www.jetbrains.com/help/webstorm/working-with-the-ide-features-from-command-line.html#toolbox)

![toolbox-settings](/assets/mac-setup/toolbox-settings.png)

![script](/assets/mac-setup/script.png)

这样就可以像打开 VS Code 一样使用 `code .` 打开 WebStorm 了

最后，祝大家都能愉快写码（完）
