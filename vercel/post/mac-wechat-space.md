---
category: 技术
title: mac 下转移微信的缓存目录
modified: 2025-03-23T21:29:19+08:00
created: 2025-03-23T18:21:47+08:00
slug: mac-wechat-space
permalink: post/mac-wechat-space
---
> [!important] 目录
> 
> - [[#破除沙盒模式]]
> - [[#建立软链接]]

### 破除沙盒模式

```Shell
sudo codesign --sign - --force --deep /Applications/WeChat.app
```

使用codesign工具对WeChat.app进行代码签名,主要作用有:

1. 绕过WeChat的沙盒限制  
    WeChat默认有苹果施加的沙盒限制,不能访问除自身目录外的文件。使用codesign重新对其签名,可以临时解除这个限制。  
    
2. 允许WeChat加载非App Store插件  
    重新签名可以让WeChat运行一些非官方的第三方插件,如微信小助手等。  
    
3. 解除对库文件的限制  
    可以让WeChat加载一些系统库文件,而不仅限于自身目录。  
    
4. 避免运行时弹出警告  
    有时候会出现运行时签名错误,重新签名可以避免弹窗警告。  
    
5. 绕过对深度链接的限制  
    WeChat不允许深度链接到App内部,重新签名可取消限制。  
    所以简单来说,这个命令可以通过重新签名的方式,解除WeChat默认的多项沙盒安全限制,使其可以加载非官方代码和访问外部文件资源。需要注意只是临时有效,重启后会失效。  
    

### 建立软链接

通过建立软链接，转移微信存储目录到其他硬盘

```Shell
cd /Users/look/Library/Containers/com.tencent.xinWeChat/Data/Library/Application\ Support
ln -s /Volumes/2023/Application\ Support/com.tencent.xinWeChat com.tencent.xinWeChat
```

###