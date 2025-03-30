---
category: 技术
tags:
  - lxc
  - nvidia
  - pve
description: 当谈论 GPU 直通时，通常是关于 PCIe 直通：将 PCIe 设备从主机传递到来宾 VM，以便来宾完全控制该设备。然而，这限制了我们只能将设备传递给单个 VM。主机失去对通过的 GPU 的所有访问权限。这不仅意味着主机无法使用该设备，而且也无法将其传递给其他虚拟机。当传递到 LXC 容器时，主机操作系统负责处理设备通信，因此多个 LXC 容器可以访问 GPU。
slug: pve-lxc-nvidia
title: PVE 在 LXC 中使用Nvidia独显硬解
modified: 2025-03-23T20:04:39+08:00
created: 2025-03-23T18:21:46+08:00
permalink: post/pve-lxc-nvidia
---
> [!important] 目录
> 
> - [[#宿主机是否需要显卡驱动]]
> - [[#宿主机安装独显驱动Nvidia]]
>     - [[#安装内核头文件及其他必要工具]]
>     - [[#查看安装情况]]
> - [[#映射设备到Lxc容器]]
> - [[#虚拟机安装驱动]]
> - [[#虚拟机中使用docker]]
> - [[#破解限制]]
> - [[#Nvidia GPU passthrough in LXC]]

> [!important] 该方案最终虽然能实现，但独显耗电高，实现过程繁琐，宿主机污染，不建议采用

## 宿主机是否需要显卡驱动

- 虚拟机直通设备：不需要，还可以屏蔽
- lxc：需要

## 宿主机安装独显驱动**Nvidia**

### 安装内核头文件及其他必要工具

```Shell
apt-get install sudo git gcc make pve-headers-$(uname -r)
```

### 在[官网](https://www.nvidia.com/Download/index.aspx)查找显卡对应的最新驱动

![[nvidia_driver_downloads.png|nvidia_driver_downloads.png]]

```Shell
mkdir /opt/nvidia
cd /opt/nvidia
wget https://download.nvidia.com/XFree86/Linux-x86_64/xxx.xx/NVIDIA-Linux-x86_64-xxx.xxx.run
chmod +x NVIDIA-Linux-x86_64-xxx.xx.run
./NVIDIA-Linux-x86_64-418.56.run --no-questions --ui=none --disable-nouveau
```

> Nouveau 是一个开源的 NVIDIA 显卡驱动项目,但Nouveau 驱动无法提供 NVIDIA 显卡的最佳性能和全部功能,特别是对较新的显卡,很多显卡特有的功能它都无法支持。所以,当你想安装 NVIDIA 的官方闭源驱动时,需要禁用 Nouveau 驱动,否则它会与 NVIDIA 的驱动冲突。

在安装 NVIDIA 显卡驱动时,使用`--disable-nouveau`，会自动生成`/etc/modprobe.d/nvidia-installer-disable-nouveau.conf`

```Shell
blacklist nouveau
options nouveau modeset=0
```

可能需要重启后再继续安装

> 在 Debian 系统上安装 NVIDIA 显卡驱动后,是否需要手动加载内核模块 `nvidia` 取决于你的安装方式。1. 如果你使用的是 Debian 提供的 `nvidia-driver` 包进行安装,则无需手动加载内核模块。因为:- Debian 的 `nvidia-driver` 包会自动构建和安装内核模块 `nvidia.ko`;- Debian 也会自动在系统启动时通过 `/etc/modules` 文件加载 `nvidia` 模块。所以,使用 `nvidia-driver` 包安装后,内核模块会自动加载,你无需手动操作。2. 如果你通过 NVIDIA 的 runfile 文件(如 `.run` 文件)手动安装的最新驱动,情况会有些不同:- NVIDIA 的运行文件同样会构建和安装 `nvidia.ko` 内核模块;- 但是,它不会自动在系统引导时加载该模块。所以,这种情况下,你需要手动通过以下两种方式之一加载 `nvidia` 内核模块:1)在 `/etc/modules` 文件中添加 `nvidia`,这样系统启动时会自动加载该模块:
> 
> ```Plain
> echo "nvidia" | sudo tee -a /etc/modules
> ```
> 
> 2)手动通过 `modprobe` 命令加载内核模块:
> 
> ```Plain
> bash
> sudo modprobe nvidia
> ```
> 
> 然后,为了在每次系统启动时自动加载这个模块,你还需要运行:
> 
> ```Plain
> bash
> echo "nvidia" | sudo tee /etc/modprobe.d/nvidia.conf
> ```
> 
> 这会产生一个 `/etc/modprobe.d/nvidia.conf` 文件,在系统引导时通过该文件自动加载 `nvidia` 模块。所以,总结来说,如果你是通过 Debian 的 `nvidia-driver` 包安装的 NVIDIA 驱动,则无需手动加载内核模块;而如果你使用 NVIDIA 的 runfile 手动安装的驱动,则需要手动加载内核模块 `nvidia`,并配置在系统启动时自动加载。推荐使用 Debian 官方提供的驱动安装方式,这样可以避免很多手动配置的麻烦。但如果需要最新驱动,手动安装也是可选方案。

### 查看安装情况

```Shell
nvidia-smi

+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.56       Driver Version: 418.56       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 980 Ti  Off  | 00000000:43:00.0 Off |                  N/A |
| 23%   61C    P0    72W / 275W |      0MiB /  6075MiB |      2%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

## 映射设备到Lxc容器

定位宿主机（PVE）中的设备id

```Plain
$ ls -l /dev/nvidia*
crw-rw-rw- 1 root root 195, 254 Dec 22 20:51 /dev/nvidia-modeset
crw-rw-rw- 1 root root 243,   0 Dec 22 20:51 /dev/nvidia-uvm
crw-rw-rw- 1 root root 243,   1 Dec 22 20:51 /dev/nvidia-uvm-tools
crw-rw-rw- 1 root root 195,   0 Dec 22 20:51 /dev/nvidia0
crw-rw-rw- 1 root root 195, 255 Dec 22 20:51 /dev/nvidiactl
```

修改容器的配置文件 `/etc/pve/lxc/<id>.conf`，添加

> 驱动版本、设备不同，每人需要映射的文件可能不同，根据自己实际情况修改

```Shell
# Allow cgroup access
lxc.cgroup2.devices.allow: c 195:* rwm
lxc.cgroup2.devices.allow: c 243:* rwm

# Pass through device files
lxc.mount.entry: /dev/nvidia0 dev/nvidia0 none bind,optional,create=file
lxc.mount.entry: /dev/nvidiactl dev/nvidiactl none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-uvm dev/nvidia-uvm none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-modeset dev/nvidia-modeset none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-uvm-tools dev/nvidia-uvm-tools none bind,optional,create=file
```

## 虚拟机安装驱动

驱动同宿主机，从官网下载，安装参数使用`-no-kernel-module`，会提示已经安装，一路选择yes 覆盖过去

  

## 虚拟机中使用docker

需要安装nvidia-docker

```Plain
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -Lhttps://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -Lhttps://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update && sudo apt-get install -y nvidia-docker2
```

## 破解限制

Nvidia 将其消费者 GPU 限制为 3 个并发会话。这意味着 1 个容器中的 3 个转码作业，或 3 个容器中的每个容器中的 1 个作业——都没有关系。这仅适用于活动会话，因此理论上您可以将 GPU 传递给 10 个容器，只要同时只有 3 个容器使用它即可。

https://github.com/keylase/nvidia-patch

## **Nvidia GPU passthrough in LXC**  
  

> [!info] Linux Containers - LXC - Manpages - lxc.container.conf.5  
>  
> [https://linuxcontainers.org/lxc/manpages//man5/lxc.container.conf.5.html](https://linuxcontainers.org/lxc/manpages//man5/lxc.container.conf.5.html)  

> [!info] NvidiaGraphicsDrivers - Debian Wiki  
>  
> [https://wiki.debian.org/NvidiaGraphicsDrivers](https://wiki.debian.org/NvidiaGraphicsDrivers)  

> [!info] Nvidia GPU passthrough in LXC  
> GPU Passthrough has become a great way to run a Linux host, but still run games under Windows.  
> [https://theorangeone.net/posts/lxc-nvidia-gpu-passthrough/](https://theorangeone.net/posts/lxc-nvidia-gpu-passthrough/)  

> [!info] Plex HW acceleration in LXC container - anyone with success?  
> Note: I don’t use this setup anymore, sharing my GPU just became not worth it with my latest hardware.  
> [https://forums.plex.tv/t/plex-hw-acceleration-in-lxc-container-anyone-with-success/219289/35](https://forums.plex.tv/t/plex-hw-acceleration-in-lxc-container-anyone-with-success/219289/35)  

> [!info] Hardware transcoding inside an unprivileged LXC container on Proxmox  
> I usually run unprivileged LXC containers on Proxmox (as opposed to privileged ones).  
> [https://ketanvijayvargiya.com/302-hardware-transcoding-inside-an-unprivileged-lxc-container-on-proxmox/](https://ketanvijayvargiya.com/302-hardware-transcoding-inside-an-unprivileged-lxc-container-on-proxmox/)  

###