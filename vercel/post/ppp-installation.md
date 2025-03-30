---
category: 技术
tags:
  - alpine
slug: ppp-installation
---
> [!important] 目录
> 
> - [[#]]
> - [[#]]

> [!info] PPP - Alpine Linux  
>  
> [https://wiki.alpinelinux.org/wiki/PPP](https://wiki.alpinelinux.org/wiki/PPP)  

```Shell
pkill pppd
pppd call chinanet
grep pppd /var/log/messages
ifup pppd
```

```Shell
user "xxxx"
plugin rp-pppoe.so eth1
noipdefault
defaultroute
noauth
persist
```

###