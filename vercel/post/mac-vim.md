---
category: 技术
tags:
  - vim
description: mac 下 vim 拷贝粘贴无法共享到系统剪贴板，使用不便，特搜索解决记录方案，记录如下
slug: mac-vim
title: Mac 下 vim 共享系统剪贴板
modified: 2025-03-30T11:27:32+08:00
created: 2025-03-23T18:21:47+08:00
permalink: post/mac-vim
---
> [!important] 目录
> 
> - [[# vim 拷贝无法使用系统剪贴板]]
> - [[#解决办法]]
> - [[#具体解释]]

### vim 拷贝无法使用系统剪贴板

传统搜索得到的结果都是 使用`vim --version | grep clipboard`  
  

```Bash
vim --version | grep clipboard
+clipboard         +keymap            +printer           +vertsplit
+ex_extra          +mouse_netterm     +syntax            -xterm_clipboard
```

看是否支持 clipboard，事实上 这个选项并没有任何直接关系，可能是比较古老的陈旧方案

### 解决办法

```Bash
echo "set clipboard=unnamed" >> ~/.vimrc
```

### 具体解释

可直接在 vim 中查看帮助

```Bash
:h 'cb'
```

> [!info] What is difference between Vim's clipboard "unnamed" and "unnamedplus" settings?  
> What is the difference between these 2 settings?  
> [https://stackoverflow.com/questions/30691466/what-is-difference-between-vims-clipboard-unnamed-and-unnamedplus-settings](https://stackoverflow.com/questions/30691466/what-is-difference-between-vims-clipboard-unnamed-and-unnamedplus-settings)