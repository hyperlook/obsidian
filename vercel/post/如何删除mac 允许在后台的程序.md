---
category: FAQ
tags:
  - mac
description: 如何删除mac 允许在后台的程序很多app虽然已经卸载了，却留下垃圾不清理，很是烦人，如下图留在允许后台可运行中的垃圾
slug: launch-daemons-agents
title: 如何删除mac 允许在后台的程序
---
> [!important] 目录
> 
> - [[#如何删除mac 允许在后台的程序]]
> - [[#参考]]

### 如何删除mac 允许在后台的程序

很多app虽然已经卸载了，却留下垃圾不清理，很是烦人，

如下图留在允许后台可运行中的垃圾

![[background_app.png|background_app.png]]

影响允许后台的目录整理如下

> /Library/LaunchDaemons  
> /Library/LaunchAgents  
> ~/Library/LaunchAgents  
> /private/var/root/Library/LaunchAgents  
> /private/var/root/Library/LaunchDaemons  

```Shell
sudo find /Library/LaunchDaemons /Library/LaunchAgents ~/Library/LaunchAgents /private/var/root/Library/LaunchAgents /private/var/root/Library/LaunchDaemons -name "*.plist" > ~/Desktop/launch.txt
```

先将所有影响启动的文件输出到桌面launch.txt文件，挨个排查

更详细的输出，可用下面这个命令

```Shell
sudo -- bash -c 'echo " - $(date) -"; while IFS= read -r eachPlist; do echo "-$eachPlist";  /usr/bin/defaults read "$eachPlist"; done <<< "$(/usr/bin/find /Library/LaunchDaemons /Library/LaunchAgents ~/Library/LaunchAgents /private/var/root/Library/LaunchAgents /private/var/root/Library/LaunchDaemons -name "*.plist")"; /usr/bin/defaults read com.apple.loginWindow LogoutHook; /usr/bin/defaults read com.apple.loginWindow LoginHook' > ~/Desktop/launch.txt
```

可能有部分启动目录不是所有版本，所有人都有，所以中间有警告可以忽略

对照launch.txt 的文件，挨个删除不需要的后台允许项

### 参考

> [!info] 如何删除「允许在后台」下面的app - Apple 社区  
>  
> [https://discussionschinese.apple.com/thread/254445461](https://discussionschinese.apple.com/thread/254445461)