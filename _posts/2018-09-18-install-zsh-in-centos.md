---
layout:         post
title:          "CentOS 安装 zsh"
subtitle:       "将zsh替换原生shell"
data:           2018-09-18
author:         "William"
header-img:     "img/post-bg-install-zsh.jpg"
tags:
    - Softwares
    - How to
---

## Catalog

{:toc}

在Linux中默认的bash没有颜色区分，界面比较朴素，可以更换为zsh使得shell界面更美观。

## 1. 安装zsh包

- -y: 表示所有的请求都为yes

```sh
yum -y install zsh
```



## 2. 切换默认shell为zsh

```sh
chsh -s /bin/zsh
```



- 在自己的腾讯云服务器中，直接执行以上指令会报一下错误：

```sh
Changing shell for root.
chsh: Warning: "/bin/zsh" is not listed in /etc/shells.
chsh: Shell not changed.
```



- 解决办法：

> Reference: [Biapy’s Answer in StackExchange](https://unix.stackexchange.com/questions/111365/how-to-change-default-shell-to-zsh-chsh-says-invalid-shell)

```sh
# Add zsh to /etc/shells:
command -v zsh | sudo tee -a /etc/shells

# You can now use chsh to set zsh as shell:
sudo chsh -s "$(command -v zsh)" "${USER}"
```



## 3. 重启服务器使配置生效

```sh
reboot
```



## 4. 安装oh my zsh

- 首先服务器需要安装git包

```sh
yum -y install git	# CentOS
```

- 正式安装

  - curl

  ```sh
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
  ```

  - wget

  ```sh
  sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
  ```

- 到这里oh my zsh就已经安装在服务器上了



## 5. 查看oh my zsh的主题列表

- 查看zsh的主题列表，可以更换自己喜欢的主题

```sh
ls ~/.oh-my-zsh/themes
```

- 设置自己喜欢的主题

```sh
vim ~/.zshrc
```

- 在~/.zshrc文件中，设置*ZSH_THEME*

```bash
ZSH_THEME="robbyrussell"	# Default zsh theme
```

