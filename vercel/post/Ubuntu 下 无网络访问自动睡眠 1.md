---
category: 技术
tags:
  - ubuntu
description: 通过vnstat 比较定时任务执行时间内的网络流量差异，确认无网络调用 pm-utils 的工具，实现自动睡眠
slug: Ubuntu-suspend
title: Ubuntu 下 无网络访问自动睡眠
---
> [!important] 目录
> 
> - [[#安装 pm-utils ]]
> - [[#安装vnstat]]
> - [[#无流量自动睡眠]]
> - [[#新建 crontab 任务]]
> - [[#]]

### 安装 pm-utils

> pm-utils 是一个用于电源管理的工具包，主要用于 Linux 系统上实现休眠（hibernation）和挂起（suspend）功能。在 Ubuntu 等基于 Debian 的发行版中，pm-utils 提供了一套脚本和实用程序，可以方便地管理系统的电源状态。

```Bash
sudo atp install pm-utils 
```

**pm-utils 的常用命令**

- pm-suspend：将系统挂起到内存。
- pm-hibernate：将系统休眠到磁盘。
- pm-suspend-hybrid：执行混合休眠。
- pm-is-supported：检查系统是否支持挂起或休眠。

### 安装vnstat

> vnstat 是一个网络流量监控工具，可以记录和查看网络接口的流量统计信息

```Bash
sudo apt install vnstat
```

**使用 vnstat 查看实时流量:**

- `vnstat -l`
- `sudo vnstat -u -i enp3s0`

### 无流量自动睡眠

```Bash
sudo vi /etc/pm/sleep.d/99_check_network_traffic.sh
```

```Bash
#!/bin/bash

# 设置调试日志文件路径
DEBUG_LOG="/var/log/pm_suspend_debug.log"
# 设置网络流量文件路径
NETWORK_TRAFFIC_FILE="/tmp/network_traffic"
PREV_NETWORK_TRAFFIC_FILE="/tmp/prev_network_traffic"

# 记录脚本执行的时间到调试日志
echo "Script executed at: $(date)" >> $DEBUG_LOG

# 检查是否有活跃的 SSH 会话
ACTIVE_SSH_SESSIONS=$(ss -tnp | grep 'sshd' | grep 'ESTAB' | wc -l)

# 如果有活跃的 SSH 会话，记录到调试日志并退出脚本
if [[ $ACTIVE_SSH_SESSIONS -gt 0 ]]; then
    echo "Active SSH sessions detected. System will not suspend." >> $DEBUG_LOG
    exit 0
fi

# 获取当前网络流量统计
CURRENT_RX=$(vnstat --oneline | awk -F';' '{print $11}' | awk '{print $1}')
CURRENT_TX=$(vnstat --oneline | awk -F';' '{print $13}' | awk '{print $1}')

# 记录当前网络流量到调试日志
echo "CURRENT_RX: $CURRENT_RX" >> $DEBUG_LOG
echo "CURRENT_TX: $CURRENT_TX" >> $DEBUG_LOG

# 检查之前的网络流量统计
if [[ -f $PREV_NETWORK_TRAFFIC_FILE ]]; then
    # 读取之前保存的网络流量数据
    read PREV_RX PREV_TX < $PREV_NETWORK_TRAFFIC_FILE

    # 记录之前的网络流量到调试日志
    echo "PREV_RX: $PREV_RX" >> $DEBUG_LOG
    echo "PREV_TX: $PREV_TX" >> $DEBUG_LOG
    
    # 计算接收和发送流量的差值
    RX_DIFF=$(echo "$CURRENT_RX - $PREV_RX" | bc)
    TX_DIFF=$(echo "$CURRENT_TX - $PREV_TX" | bc)
    
    # 记录差值到调试日志
    echo "RX difference: $RX_DIFF" >> $DEBUG_LOG
    echo "TX difference: $TX_DIFF" >> $DEBUG_LOG
    
    # 如果接收和发送流量差值都为零，则挂起系统
    if [[ $RX_DIFF == 0 && $TX_DIFF == 0 ]]; then
        echo "No network traffic. Suspending system." >> $DEBUG_LOG
        sudo systemctl suspend
    else
        echo "Network traffic detected. System will not suspend." >> $DEBUG_LOG
    fi
else
    echo "Previous network traffic data not found." >> $DEBUG_LOG
fi

# 保存当前网络流量统计到临时文件
echo "$CURRENT_RX $CURRENT_TX" > $NETWORK_TRAFFIC_FILE
echo "Current network traffic written to $NETWORK_TRAFFIC_FILE" >> $DEBUG_LOG

# 如果之前的网络流量文件存在，删除它
if [[ -f $PREV_NETWORK_TRAFFIC_FILE ]]; then
    rm $PREV_NETWORK_TRAFFIC_FILE
    echo "Previous network traffic file $PREV_NETWORK_TRAFFIC_FILE deleted" >> $DEBUG_LOG
fi

# 将当前网络流量文件重命名为之前的网络流量文件
mv $NETWORK_TRAFFIC_FILE $PREV_NETWORK_TRAFFIC_FILE
echo "Current network traffic file renamed to $PREV_NETWORK_TRAFFIC_FILE" >> $DEBUG_LOG
```

  

```Bash
sudo chmod+x /etc/pm/sleep.d/99_check_network_traffic.sh
```

### 新建 crontab 任务

```Bash
sudo crontab -e

#5 分钟执行一次
*/5 * * * * /etc/pm/sleep.d/99_check_network_traffic.sh
```

  

###