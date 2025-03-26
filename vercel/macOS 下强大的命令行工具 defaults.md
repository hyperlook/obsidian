---
title: macOS 下强大的命令行工具 defaults
tags:
  - mac
  - defaults
description: defaults 是 macOS 系统中一个非常强大的命令行工具，用于读取、写入和管理 macOS 的偏好设置（Preferences）。偏好设置是以 .plist 文件的形式存储在系统中的，defaults 命令可以方便地操作这些文件，而无需手动编辑它们。
created: 2025-03-26 09:10
modified: 2025-03-26T11:46:55+08:00
draft: false
permalink: mac/defaults
---
`defaults` 是 macOS 系统中一个非常强大的命令行工具，用于读取、写入和管理 macOS 的偏好设置（Preferences）。偏好设置是以 `.plist` 文件的形式存储在系统中的，`defaults` 命令可以方便地操作这些文件，而无需手动编辑它们。

### 1. **基本用途**
`defaults` 命令主要用于：
- 查看和修改应用程序的偏好设置。
- 调整系统级别的配置选项。
- 自动化配置任务，例如通过脚本批量修改设置。

偏好设置文件通常存储在以下路径：
- 用户级别：`~/Library/Preferences/`
- 系统级别：`/Library/Preferences/`

### 2. **常用语法**
`defaults` 命令的基本语法如下：
```bash
defaults [操作] [域] [键] [值]
```

#### 参数说明：
- **操作**：指定要执行的操作，例如 `read`、`write`、`delete` 等。
- **域**：指定目标偏好设置的域，通常是反向域名格式（如 `com.apple.finder`）。
- **键**：指定偏好设置中的键名。
- **值**：指定要写入或修改的值。

---

### 3. **常用操作**

#### （1）查看偏好设置
使用 `read` 操作可以查看特定域或键的值。

- 查看某个域的所有设置：
  ```bash
  defaults read <域>
  ```
  示例：
  ```bash
  defaults read com.apple.finder
  ```

- 查看某个键的具体值：
  ```bash
  defaults read <域> <键>
  ```
  示例：
  ```bash
  defaults read com.apple.finder AppleShowAllFiles
  ```

#### （2）写入或修改偏好设置
使用 `write` 操作可以添加或修改某个键的值。

- 写入布尔值：
  ```bash
  defaults write <域> <键> -bool <true/false>
  ```
  示例：
  ```bash
  defaults write com.apple.finder AppleShowAllFiles -bool true
  ```

- 写入字符串：
  ```bash
  defaults write <域> <键> -string "<值>"
  ```
  示例：
  ```bash
  defaults write com.apple.screencapture name -string "MyScreenshot"
  ```

- 写入整数：
  ```bash
  defaults write <域> <键> -int <值>
  ```
  示例：
  ```bash
  defaults write com.apple.dock tilesize -int 64
  ```

#### （3）删除偏好设置
使用 `delete` 操作可以删除某个键或整个域的设置。

- 删除某个键：
  ```bash
  defaults delete <域> <键>
  ```
  示例：
  ```bash
  defaults delete com.apple.finder AppleShowAllFiles
  ```

- 删除整个域：
  ```bash
  defaults delete <域>
  ```
  示例：
  ```bash
  defaults delete com.apple.finder
  ```

#### （4）列出所有域
列出当前用户的偏好设置域：
```bash
defaults domains
```

---

### 4. **注意事项**
- **权限问题**：某些系统级别的偏好设置可能需要管理员权限（使用 `sudo`）。
- **重启生效**：修改某些设置后，可能需要重启相关应用程序或系统才能生效。例如，修改 Finder 设置后可以使用以下命令重启 Finder：
  ```bash
  killall Finder
  ```
- **备份数据**：在修改系统设置之前，建议备份相关的 `.plist` 文件，以免出现意外问题。

---

### 5. **实际应用场景**

#### （1）显示隐藏文件
启用 Finder 显示隐藏文件：
```bash
defaults write com.apple.finder AppleShowAllFiles -bool true
killall Finder
```
禁用：
```bash
defaults write com.apple.finder AppleShowAllFiles -bool false
killall Finder
```

#### （2）更改截图保存位置
将截图保存到桌面以外的目录：
```bash
defaults write com.apple.screencapture location ~/Documents/Screenshots
killall SystemUIServer
```

#### （3）调整 Dock 图标大小
将 Dock 图标大小调整为 36 像素：
```bash
defaults write com.apple.dock tilesize -int 36
killall Dock
```

---

### 6. **总结**
`defaults` 命令是 macOS 中管理偏好设置的强大工具，适合高级用户和开发者用来定制系统行为或自动化配置任务。然而，由于它直接操作底层配置文件，因此在使用时需要谨慎，避免对系统造成不必要的影响。
