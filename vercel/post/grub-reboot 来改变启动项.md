---
category: 技术
tags:
  - grub
  - ubuntu
description: 我想通过 grub-reboot 来改变启动项， 帮我看下 Ubuntu 和 windows 对应的条目数为几
slug: grub-reboot
title: grub-reboot 来改变启动项
permalink: post/grub-reboot
---
> [!important] 目录
> 
> - [[#从grub.cfg 来确认Ubuntu 和 windows 对应的条目]]
> - [[#缩短grub 菜单时间]]
> - [[#grub-reboot 选中启动项]]
> - [[#其他常用命令]]

> 需求：我的 PC 同时装有 Ubuntu 和 windows11，如何做到不接显示器，盲操作切换，现在默认启动的是 Ubuntu

可以通过grub-reboot 临时切换要启动的系统

### 从grub.cfg 来确认Ubuntu 和 windows 对应的条目

```Bash
grep 'menuentry ' /boot/grub/grub.cfg
```

这将会列出 GRUB 配置中的所有启动项，格式如下：

```Bash
menuentry 'Ubuntu' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-df8603eb-abb4-46b9-87c9-e8d9ed5f7fdd' {
menuentry 'Advanced options for Ubuntu' $menuentry_id_option 'gnulinux-advanced-df8603eb-abb4-46b9-87c9-e8d9ed5f7fdd' {
menuentry 'Windows Boot Manager (on /dev/nvme0n1p1)' --class windows --class os $menuentry_id_option 'osprober-efi-36B4-BE16' {
menuentry 'System setup' $menuentry_id_option 'uefi-firmware' {
```

假设输出是这样的，你可以看到不同的启动项编号：

```Plain
0.	Ubuntu 正常模式
1.	Ubuntu 高级选项
2.	Windows Boot Manager
3.	BIOS 设置
```

### 缩短grub 菜单时间

sudo vim /etc/default/grub

编辑GRUB_TIMEOUT  
更新sudo update-grub  

  

###   
grub-reboot 选中启动项  

- 重启时启动 Windows，可以运行以下命令：
    
    ```Bash
    sudo grub-reboot 2
    sudo reboot
    ```
    
- 置为启动 Ubuntu，可以运行以下命令：
    
    ```Bash
    sudo grub-reboot 0
    sudo reboot
    ```
    

### 其他常用命令

```Bash
sudo grub-editenv list
```