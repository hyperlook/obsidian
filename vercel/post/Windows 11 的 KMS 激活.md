---
category: 技术
tags:
  - kms
  - windows
  - 激活
description: KMS 激活KMS（Key Management Service）是微软推出的一种用于激活Microsoft Windows和Microsoft Office的技术。它可以在企业内部搭建激活服务器，使得企业用户无需向Microsoft购买授权即可激活使用Microsoft Windows和Microsoft Office等软件产品
slug: Windows11-kms-activate
title: Windows 11 的 KMS 激活
modified: 2025-03-23T19:47:23+08:00
created: 2025-03-23T18:21:47+08:00
---
> [!important] 目录
> 
> - [[#KMS 激活]]
> - [[#Windows 11 KMS 激活步骤]]
> - [[#Windows 10/11 产品密钥 cd-key]]
> - [[#自建kms 服务器端]]

### **KMS 激活**

KMS（Key Management Service）是微软推出的一种用于激活Microsoft Windows和Microsoft Office的技术。它可以在企业内部搭建激活服务器，使得企业用户无需向Microsoft购买授权即可激活使用Microsoft Windows和Microsoft Office等软件产品。

KMS客户端需要与KMS服务器定期通信以保持激活状态。默认情况下，客户端将每7天尝试与KMS服务器通信一次。如果客户端无法连接到KMS服务器，Windows将在激活有效期后进入非激活状态。客户端仅需要在激活期间保持连接，而不是一直连接。

### Windows 11 KMS 激活步骤

> [!info] Key Management Services (KMS) client activation and product keys for Windows Server and Windows  
> Get the product keys needed for setup and activation of Windows Server and other Windows products from a KMS host server.  
> [https://learn.microsoft.com/en-us/windows-server/get-started/kms-client-activation-keys](https://learn.microsoft.com/en-us/windows-server/get-started/kms-client-activation-keys)  

以下是 Windows 11 专业版 KMS 激活的步骤：

> 以下命令需要在管理员权限下运行

```PowerShell
slmgr.vbs /upk

slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX

slmgr /skms kms.loli.best

slmgr /ato
```

```PowerShell
slmgr.vbs -dlv
slmgr.vbs -xpr
```

### **Windows 10/11 产品密钥 cd-key**

> [!important] 目前微软只发布了半年期
> 
> **Product Key，windows 10/11 kms 激活的有效期为180天**

|   |   |
|---|---|
|操作系统|KMS激活序列号|
|Windows 10/11 Professional|W269N-WFGWX-YVC9B-4J6C9-T83GX|
|Windows 10/11 Professional N|MH37W-N47XK-V7XM9-C7227-GCQG9|
|Windows 10/11 Pro for Workstations|NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J|
|Windows 10/11 Pro for Workstations N|9FNHH-K3HBT-3W4TD-6383H-6XYWF|
|Windows 10/11 Pro Education|6TP4R-GNPTD-KYYHQ-7B7DP-J447Y|
|Windows 10/11 Pro EducationN|YVWGF-BXNMC-HTQYQ-CPQ99-66QFC|
|Windows 10/11 Education|NW6C2-QMPVW-D7KKK-3GKT6-VCFB2|
|Windows 10/11 Education N|2WH4N-8QGBV-H22JP-CT43Q-MDWWJ|
|Windows 10/11 Enterprise|NPPR9-FWDCX-D2C8J-H872K-2YT43|
|Windows 10/11 Enterprise N|DPH2V-TTNVB-4X9Q3-TJR4H-KHJW4|
|Windows 10/11 Enterprise G|YYVX9-NTFWV-6MDM3-9PT4T-4M68B|
|Windows 10/11 Enterprise G N|44RPN-FTY23-9VTTB-MP9BX-T84FV|

### 自建kms 服务器端

- 使用docker 快速搭建[^1]
    
    ```PowerShell
    docker run -d -p 1688:1688 --name kms --restart=always teddysun/kms
    ```
    
- 使用带kms的openwrt 路由

  

---

  



> [!info] 参考
>  https://github.com/luodaoyi/kms-server
>  
>  https://github.com/Wind4/vlmcsd




[^1]: https://hub.docker.com/r/teddysun/kms
