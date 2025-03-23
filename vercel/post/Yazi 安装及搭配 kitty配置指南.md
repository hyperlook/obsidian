---
title: Yazi 安装及搭配 kitty配置指南
category: 技术
tags:
  - ubuntu
  - yazi
description: Yazi 配合 kitty ，实现快速下载服务器上文件 和 拷贝文本到本机剪切板
slug: yazi-kitty
---
> [!important] 目录
> 
> - [[# 安装]]
> - [[#配置]]

### 安装

[https://yazi-rs.github.io/docs/installation#official-binaries](https://yazi-rs.github.io/docs/installation#official-binaries)

Mac

```Bash
brew install yazi
```

Ubuntu

```Bash
wget https://github.com/sxyazi/yazi/releases/download/v0.2.5/yazi-x86_64-unknown-linux-gnu.zip
```

```Bash
sudo mv yazi /usr/local/bin/
```

### 配置

[https://yazi-rs.github.io/docs/quick-start/](https://yazi-rs.github.io/docs/quick-start/)

- 启动 Yazi - 运行 yy ，目的 退出 Yazi 时切换到当前工作目录
    
    ```Bash
    function yy() {
    	local tmp="$(mktemp -t "yazi-cwd.XXXXXX")"
    	yazi "$@" --cwd-file="$tmp"
    	if cwd="$(cat -- "$tmp")" && [ -n "$cwd" ] && [ "$cwd" != "$PWD" ]; then
    		cd -- "$cwd"
    	fi
    	rm -f -- "$tmp"
    }
    ```
    
- 按 ~ 打开帮助菜单
    
    可看到所有的快捷键，包括自定义的都可以在这看到
    

- 自定义配置[https://yazi-rs.github.io/docs/configuration/keymap#manager.shell](https://yazi-rs.github.io/docs/configuration/keymap#manager.shell)
    
    ```Bash
    # A TOML linter such as https://taplo.tamasfe.dev/ can use this schema to validate your config.
    # If you encounter any issues, please make an issue at https://github.com/yazi-rs/schemas.
    "$schema" = "https://yazi-rs.github.io/schemas/yazi.json"
    
    [manager]
    ratio          = [ 1, 3, 4 ]
    sort_by        = "alphabetical"
    sort_sensitive = false
    sort_reverse   = false
    sort_dir_first = true
    linemode       = "size"
    show_hidden    = false
    show_symlink   = true
    scrolloff      = 5
    
    [preview]
    tab_size        = 2
    max_width       = 600
    max_height      = 900
    cache_dir       = ""
    image_filter    = "triangle"
    image_quality   = 75
    sixel_fraction  = 15
    ueberzug_scale  = 1
    ueberzug_offset = [ 0, 0, 0, 0 ]
    
    [opener]
    edit = [
    	{ run = '${EDITOR:=vi} "$@"', desc = "$EDITOR", block = true, for = "unix" },
    	{ run = 'code "%*"',    orphan = true, desc = "code",         for = "windows" },
    	{ run = 'code -w "%*"', block = true,  desc = "code (block)", for = "windows" },
    ]
    open = [
    	{ run = 'xdg-open "$@"',                desc = "Open", for = "linux" },
    	{ run = 'open "$@"',                    desc = "Open", for = "macos" },
    	{ run = 'start "" "%1"', orphan = true, desc = "Open", for = "windows" },
    ]
    reveal = [
    	{ run = 'xdg-open "$(dirname "$0")"',            desc = "Reveal", for = "linux" },
    	{ run = 'open -R "$1"',                          desc = "Reveal", for = "macos" },
    	{ run = 'explorer /select, "%1"', orphan = true, desc = "Reveal", for = "windows" },
    	{ run = '''exiftool "$1"; echo "Press enter to exit"; read _''', block = true, desc = "Show EXIF", for = "unix" },
    ]
    extract = [
    	{ run = 'unar "$1"', desc = "Extract here", for = "unix" },
    	{ run = 'unar "%1"', desc = "Extract here", for = "windows" },
    ]
    play = [
    	{ run = 'mpv "$@"', orphan = true, for = "unix" },
    	{ run = 'mpv "%1"', orphan = true, for = "windows" },
    	{ run = '''mediainfo "$1"; echo "Press enter to exit"; read _''', block = true, desc = "Show media info", for = "unix" },
    ]
    
    [open]
    rules = [
    	{ name = "*/", use = [ "edit", "open", "reveal" ] },
    
    	{ mime = "text/*",          use = [ "edit", "reveal" ] },
    	{ mime = "image/*",         use = [ "open", "reveal" ] },
    	{ mime = "{audio,video}/*", use = [ "play", "reveal" ] },
    	{ mime = "inode/x-empty",   use = [ "edit", "reveal" ] },
    
    	{ mime = "application/*zip", use = [ "extract", "reveal" ] },
    	{ mime = "application/x-{tar,bzip*,7z-compressed,xz,rar}", use = [ "extract", "reveal" ] },
    
    	{ mime = "application/json", use = [ "edit", "reveal" ] },
    	{ mime = "*/javascript",     use = [ "edit", "reveal" ] },
    
    	{ mime = "*", use = [ "open", "reveal" ] },
    ]
    
    [tasks]
    micro_workers    = 10
    macro_workers    = 25
    bizarre_retry    = 5
    image_alloc      = 536870912  # 512MB
    image_bound      = [ 0, 0 ]
    suppress_preload = false
    
    [plugin]
    
    preloaders = [
    	{ name = "*", cond = "!mime", run = "mime", multi = true, prio = "high" },
    	# Image
    	{ mime = "image/*", run = "image" },
    	# Video
    	{ mime = "video/*", run = "video" },
    	# PDF
    	{ mime = "application/pdf", run = "pdf" },
    ]
    previewers = [
    	{ name = "*/", run = "folder", sync = true },
    	# Code
    	{ mime = "text/*", run = "code" },
    	{ mime = "*/{xml,javascript,x-wine-extension-ini}", run = "code" },
    	# JSON
    	{ mime = "application/json", run = "json" },
    	# Image
    	{ mime = "image/vnd.djvu", run = "noop" },
    	{ mime = "image/*",        run = "image" },
    	# Video
    	{ mime = "video/*", run = "video" },
    	# PDF
    	{ mime = "application/pdf", run = "pdf" },
    	# Archive
    	{ mime = "application/*zip", run = "archive" },
    	{ mime = "application/x-{tar,bzip*,7z-compressed,xz,rar}", run = "archive" },
    	# Fallback
    	{ name = "*", run = "file" },
    ]
    
    [input]
    # cd
    cd_title  = "Change directory:"
    cd_origin = "top-center"
    cd_offset = [ 0, 2, 50, 3 ]
    
    # create
    create_title  = "Create:"
    create_origin = "top-center"
    create_offset = [ 0, 2, 50, 3 ]
    
    # rename
    rename_title  = "Rename:"
    rename_origin = "hovered"
    rename_offset = [ 0, 1, 50, 3 ]
    
    # trash
    trash_title 	= "Move {n} selected file{s} to trash? (y/N)"
    trash_origin	= "top-center"
    trash_offset	= [ 0, 2, 50, 3 ]
    
    # delete
    delete_title 	= "Delete {n} selected file{s} permanently? (y/N)"
    delete_origin	= "top-center"
    delete_offset	= [ 0, 2, 50, 3 ]
    
    # filter
    filter_title  = "Filter:"
    filter_origin = "top-center"
    filter_offset = [ 0, 2, 50, 3 ]
    
    # find
    find_title  = [ "Find next:", "Find previous:" ]
    find_origin = "top-center"
    find_offset = [ 0, 2, 50, 3 ]
    
    # search
    search_title  = "Search via {n}:"
    search_origin = "top-center"
    search_offset = [ 0, 2, 50, 3 ]
    
    # shell
    shell_title  = [ "Shell:", "Shell (block):" ]
    shell_origin = "top-center"
    shell_offset = [ 0, 2, 50, 3 ]
    
    # overwrite
    overwrite_title  = "Overwrite an existing file? (y/N)"
    overwrite_origin = "top-center"
    overwrite_offset = [ 0, 2, 50, 3 ]
    
    # quit
    quit_title  = "{n} task{s} running, sure to quit? (y/N)"
    quit_origin = "top-center"
    quit_offset = [ 0, 2, 50, 3 ]
    
    [select]
    open_title  = "Open with:"
    open_origin = "hovered"
    open_offset = [ 0, 1, 50, 7 ]
    
    [which]
    sort_by        = "none"
    sort_sensitive = false
    sort_reverse   = false
    
    [log]
    enabled = false
    
    [headsup]
    ```
    

- 自定义快捷键
    
    [https://yazi-rs.github.io/docs/configuration/keymap](https://yazi-rs.github.io/docs/configuration/keymap)
    
    ```Bash
    [[manager.prepend_keymap]]
    on   = [ "t" ]
    run  = "plugin --sync max-preview"
    desc = "Maximize or restore preview"
    
    [[manager.prepend_keymap]]
    on   = [ "<C-c>" ]
    run  = '''
    shell --confirm
    'cat "$0" | kitten clipboard'
    '''
    desc = "copy the file content to clipboard"
    
    [[input.prepend_keymap]]
    on   = [ "<Esc>" ]
    run  = "close"
    desc = "Cancel input"
    
    
    [[manager.prepend_keymap]]
    on   = [ "S-Delete" ]
    run = "remove --force"
    desc = "Permanently delete the files"
    
    [[manager.prepend_keymap]]
    on   = [ "<C-s>" ]
    run  = '''
    shell  --block --confirm
    'kitten transfer -p passwords "$@" /Users/look/Downloads/ ||  
    kitten transfer -p passwords "$0" /Users/look/Downloads/'
    '''
    desc = "Download selected files using Kitty transfer"
    
    
    ```
    
- 按 q 退出

---

> 以上部分内容需要配合 kitty 使用

> [!info] Transfer files  
> Transfer files to and from remote computers over the TTY device itself.  
> [https://sw.kovidgoyal.net/kitty/kittens/transfer/](https://sw.kovidgoyal.net/kitty/kittens/transfer/)  

> [!info] clipboard  
> Copy/paste to the system clipboard from shell scripts The clipboard kitten can be used to read or write to the system clipboard from the shell.  
> [https://sw.kovidgoyal.net/kitty/kittens/clipboard/](https://sw.kovidgoyal.net/kitty/kittens/clipboard/)