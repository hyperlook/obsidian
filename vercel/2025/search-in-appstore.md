---
category: FAQ
tags:
  - ios
  - apple
description: 我们经常在iphone，mac 上有这样的需求，需要直接能跳转到app store 的操作，比如在捷径、或raycast的quicklinks中，这就用到app store的直接跳转链接。
slug: search-in-appstore
modified: 2025-03-25T22:21:15+08:00
created: 2025-03-23T18:21:47+08:00
title: 获取应用在App Store的直链链接
---
> [!important] 目录
> 
> - [[#如何获取苹果App Store的直接打开链接]]
> - [[#app store 的搜索链接]]
> - [[#参考链接]]

我们经常在iphone，mac 上有这样的需求，需要直接能跳转到app store 的操作，比如在捷径、或raycast的quicklinks中，这就用到app store的直接跳转链接。

### 如何获取苹果App Store的直接打开链接

这里我们就需要用到[[itms-apps]]

苹果App Store的直接打开链接可以通过以下方式获得：

1. 打开 App Store 并搜索您要链接的应用程序。
2. 打开应用程序页面并复制 URL。
3. 粘贴 URL 并将“https://”更改为“itms-apps：//”即可获得直接打开链接。

例如，微信的应用ID是414478124，所以它的直接打开链接是：

[微信-appStore](itms-apps://itunes.apple.com/app/id414478124)

### app store 的搜索链接

同理，如上

```Shell
itms-apps://search.itunes.apple.com/WebObjects/MZSearch.woa/wa/search?media=software&term={query}
参考资料
```

把`{query}` 换成你需要的关键词即可

---

### 参考链接

---

> [!info] How to link to apps on the app store  
> I am creating a free version of my iPhone game.  
> [https://stackoverflow.com/questions/433907/how-to-link-to-apps-on-the-app-store](https://stackoverflow.com/questions/433907/how-to-link-to-apps-on-the-app-store)  

> [!info] Is there a direct link to an AppStore search result on iOS?  
> I'd like to link to all apps as if the user had opened the Apple App Store and searched for &quot;Authenticator&quot;.  
> [https://stackoverflow.com/questions/68442406/is-there-a-direct-link-to-an-appstore-search-result-on-ios](https://stackoverflow.com/questions/68442406/is-there-a-direct-link-to-an-appstore-search-result-on-ios)