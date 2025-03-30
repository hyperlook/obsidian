---
category: 技术
tags:
  - mac
  - terminus
slug: terminus-locale
title: mac 下 terminus 中文乱码
---
> [!important] 目录
> 
> - [[#1]]
> - [[#]]
> - [[#]]

### 1

最近在 Mac 上使用 Terminus 终端时，发现中文乱码问题。解决方法如下：

1. 打开终端，输入以下命令：
    
    ```Plain
    locale
    ```
    
    检查输出结果中是否包含以下内容：
    
    ```Plain
    LC_CTYPE="en_US.UTF-8"
    ```
    
    如果没有，需要安装中文语言包，或者修改终端的语言设置。
    
2. 如果以上方法均未能解决问题，可以尝试在 .bashrc 或 .zshrc 文件中添加以下代码：
    
    ```Plain
    export LC_ALL=en_US.UTF-8
    ```
    
    然后保存文件并重新启动终端。
    

希望这些方法可以帮助您解决 Terminus 中文乱码的问题。

LC_CTYPE 和 LC_ALL 都是用来设置系统语言环境的环境变量。

LC_CTYPE 环境变量主要用于设置字符集编码，比如设置为 "zh_CN.UTF-8" 表示使用 UTF-8 编码的中文字符集。

LC_ALL 环境变量则可以覆盖所有的语言环境变量，包括 LC_CTYPE。如果 LC_ALL 和 LC_CTYPE 同时设置了值，那么 LC_ALL 的值将覆盖 LC_CTYPE 的值。

通常情况下，我们只需要设置 LC_CTYPE 就可以解决终端中文乱码的问题。如果还有其它语言环境的问题，可以尝试设置 LC_ALL 或者其它相关的环境变量。

- `LANG` 是用于设置系统默认的语言环境的变量，一般不需要单独设置。当需要设置时，可以设置为类似 `zh_CN.UTF-8` 的格式。
- `LC_CTYPE` 是用于设置字符集编码的环境变量，一般用于解决终端中文乱码问题。比如设置为 `zh_CN.UTF-8` 表示使用 UTF-8 编码的中文字符集。
- `LC_ALL` 是用于覆盖所有语言环境变量的环境变量，包括 `LANG` 和 `LC_CTYPE`。如果同时设置了 `LC_ALL` 和 `LC_CTYPE` 的值，那么 `LC_ALL` 的值将覆盖 `LC_CTYPE` 的值。

  

en_US.UTF-8 是美国英语字符集编码，对应的是 ASCII 编码。而 zh_CN.UTF-8 是中文字符集编码，对应的是 Unicode 编码。两者的区别在于字符集的不同，en_US.UTF-8 适用于英文系统，而 zh_CN.UTF-8 适用于中文系统。在终端中，如果使用了中文字符集编码，则需要设置 LC_CTYPE 环境变量为 zh_CN.UTF-8 才能正确显示中文字符。

###