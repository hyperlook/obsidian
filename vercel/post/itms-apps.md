---
title: itms-apps 苹果的私有 URL 方案
tags:
  - apple
description: itms-apps:// 是一个由苹果定义的私有 URL 方案（URL Scheme），用于在 iOS 设备上直接唤起 App Store 应用并导航到特定内容（如应用页面或开发者页面）。
created: 2025-03-24 09:07
modified: 2025-03-24T10:10:38+08:00
draft: false
slug: itms-apps
permalink: post/itms-apps
---

itms-apps:// 是一个由苹果定义的私有 URL 方案（URL Scheme），用于在 iOS 设备上直接唤起 App Store 应用并导航到特定内容（如应用页面或开发者页面）。
>[!tip] 未公开
>由于它是苹果的私有实现，官方并未公开列出所有可能的“接口”或详细的子路径参数，因此不存在一个明确的“所有已知的 itms-apps:// 私有 API 接口”清单。开发者社区和公开文档中提到的用法，主要基于逆向工程、实际测试和苹果的部分间接指引。

现有信息整理的与 itms-apps:// 相关的已知用法和说明。这些并不是传统意义上的“API 接口”（如函数调用），而是 URL 格式的变体，用于实现特定功能：

## 已知的 itms-apps:// URL 方案用法
|   |   |
|---|---|
|**示例**|**说明**|
|`itms-apps://itunes.apple.com/app/id284882215`|特定应用页面|
|`itms-apps://itunes.apple.com/developer/id284882218`|开发者页面|
|`itms-apps://search.itunes.apple.com/WebObjects/MZSearch.woa/wa/search?q=Twitter`|搜索应用|
|`itms-apps://itunes.apple.com/`|App Store 主页。|
|`itms-apps://itunes.apple.com/cn/app/id284882215`|区域特定链接|



### 打开特定应用页面
- **格式**: itms-apps://itunes.apple.com/app/id<APP_ID>
- **说明**: 直接打开 App Store 中的某个应用页面，<APP_ID> 是应用的唯一标识符（例如 id123456789）。
- **示例**: itms-apps://itunes.apple.com/app/id284882215（打开 Facebook 应用页面）。
- **用途**: 用于引导用户下载或查看特定应用，常用于应用内推荐或更新提示。
###  打开开发者页面
- **格式**: itms-apps://itunes.apple.com/developer/id<DEVELOPER_ID>
- **说明**: 跳转到 App Store 中某个开发者的页面，展示其所有应用，<DEVELOPER_ID> 是开发者的唯一标识符。
- **示例**: itms-apps://itunes.apple.com/developer/id284882218（可能指向某个开发者的页面，具体 ID 需查询）。
- **用途**: 便于用户查看同一开发者的其他作品。
###  搜索应用
- **格式**: itms-apps://search.itunes.apple.com/WebObjects/MZSearch.woa/wa/search?q=<SEARCH_TERM>
- **说明**: 在 App Store 中执行搜索，<SEARCH_TERM> 是搜索关键词。
- **示例**: itms-apps://search.itunes.apple.com/WebObjects/MZSearch.woa/wa/search?q=Twitter
- **用途**: 引导用户搜索相关应用，但这种格式较少使用，且可能因 iOS 版本变化而失效。
###  打开 App Store 主页
- **格式**: itms-apps://itunes.apple.com/
- **说明**: 直接打开 App Store 应用的主页。
- **示例**: itms-apps://itunes.apple.com/
- **用途**: 用于简单地将用户带入 App Store 环境。
### 区域特定链接
- **格式**: itms-apps://itunes.apple.com/<COUNTRY_CODE>/app/id<APP_ID>
- **说明**: 指定国家或地区的 App Store，<COUNTRY_CODE> 是两位国家代码（如 us、cn）。
- **示例**: itms-apps://itunes.apple.com/cn/app/id284882215
- **用途**: 确保用户跳转到其所在地区的 App Store，可能影响价格或可用性显示。
