---
category: FAQ
tags:
  - vscode
description: 在.gitignore里添加了2个文件例外后，vscode 的资源管理器找不到该文件了，一番搜索解决后记录如下
slug: vscode-gitignore
---
> [!important] 目录
> 
> - [[#排查是否在exclude 列表里]]
> - [[#是否显示在.gitignore 里文件]]
> - [[#]]

在.gitignore里添加了2个文件例外后，vscode 的资源管理器找不到该文件了，一番搜索解决后记录如下

> vscode 的资源管理器中不显示该有的文件

### 排查是否在exclude 列表里

`ctr+shift+p` 打开设置后，查找exclude

![[vs_file_exclude.png|vs_file_exclude.png]]

### 是否显示在.gitignore 里文件

![[vs_gitignore_exclude.png|vs_gitignore_exclude.png]]

  

###