---
layout: post
title: Linux 程序员基础开发环境搭建和软件安装 
category: [geek-env]
author: Howie Lee
tags: [Environment,Linux,zsh,python,ruby,gcc]
---

## 终端开发环境搭建
### 安装 zsh

通过 yum 方式安装 `zsh`

```shell
sudo yum install zsh
```

### 安装 oh-my-zsh

通过如下命令安装

```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 安装 auto-jump

通过 yum 方式安装 `auto-jump`

```shell
sudo yum install autojump-zsh
```

在 `~/.zshrc` 配置文件的 plugins 项目中加入 `autojump` 配置

```shell
plugins=(git autojump)
```

### 安装 `zsh-autosuggestions`

通过 github 方式安装 zsh-autosuggestions

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
```

### 安装 zsh-syntax-highlighting

通过 github 下载安装

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
```

在 `~/.zshrc` 配置文件的 plugins 项目中加入 `zsh-syntax-highlighting` 配置

```
plugins=(git autojump zsh-syntax-highlighting)
```

在 `~/.zshrc` 中加入如下配置

```shell
# enable suggestion
source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
# 配置颜色
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=3'
```

## 通过 pyenv 搭建 python 开发环境

### 安装环境依赖

```shell
sudo yum install -y make patch gdbm-devel openssl-devel sqlite-devel readline-devel zlib-devel bzip2-devel gcc-c++
```

### 安装 pyenv

```shell
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

### 安装 python

```shell
env PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.7.3
```

### 终端环境配置

在 `~/.zshrc` 中加入如下配置「安装完 pyenv 后会提示如何添加」

```
export PATH="/home/lihy/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```


## 通过 rvm 搭建 ruby 开发环境

### 安装 rvm 

通过如下命令进行安装

```shell
# 不需要在 root 权限下执行
curl -L get.rvm.io | bash -s stable
source ~/.zshrc
```

### 更换国内源
修改 rvm 的 Ruby 安装源到 Ruby China 的 Ruby 镜像服务器

```shell
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

# 可以通过以下命令查看源是否替换成功
gem sources -l
```

### 通过 rvm 安装 ruby

查看可以通过 rvm 安装的 ruby 版本

```shell
rvm list known
```

安装上面查看得到的最新版本即可

```shell
rvm install 2.6
```


