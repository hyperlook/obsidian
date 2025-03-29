---
title: Linux下uv快速部署 comfyui
tags:
  - linux
  - comfyui
description: 
created: 2025-03-26 11:48
modified: 2025-03-29T20:05:54+08:00
draft: true
slug: comfyui-uv
---

## NVIDIA 显卡驱动

### Arch Linux 下显卡驱动安装[^2]
我用的是 Arch Linux，显卡是 4070s[^3]，查询命令
```
lspci -k -d ::03xx
```

显示AD104，属于 **Ada Lovelace** 架构（NV190/ADXXX 系列）, 选择[nvidia](https://archlinux.org/packages/?name=nvidia)包 ，安装比较简单
```
sudo pacman -S nvidia
```

### NVIDIA 显卡驱动的兼容性[^1]
 - 向后兼容性确保较新的 NVIDIA 驱动程序可以与较旧的 CUDA 工具包一起使用。
   我们安装完驱动，输入`nvidia-smi`就可以看到该驱动兼容的最高 cuda 版本
   ![[nvidia-smi.png]]
 - 次要版本和向前兼容性确保了旧的 NVIDIA 驱动程序可以与较新的 CUDA 工具包一起使用
 > 比较复杂，普通用户只需按向后兼容，把驱动搞到新版即可

## CUDA 和 PyTorch
### CUDA Toolkit
> CUDA是由英伟达所推出的一种软硬件集成技术，是该公司对于GPGPU的正式名称。透过这个技术，用户可利用NVIDIA的GPU进行图像处理之外的运算，亦是首次可以利用GPU作为C-编译器的开发环境。而我们日常称的 CUDA 通常指CUDA Toolkit。

CUDA Toolkit 是套完整的开发工具包，包括编译器（nvcc）、库（如 cuBLAS、cuFFT）和运行时（CUDA Runtime）。通过CUDA，开发者可以使用NVIDIA提供的CUDA API来编写程序，从而在支持CUDA的GPU上执行计算密集型任务，比如物理模拟、图像和视频处理、机器学习算法等。


### PyTorch 自带 CUDA 是什么？

PyTorch 的官方二进制发行版内置了一个精简的 CUDA Runtime，用于支持 PyTorch 的 GPU 计算，包含了运行时所需的 CUDA 库（如 cuDNN、cuBLAS 等）。因此只需要确保你的系统上安装了正确的 NVIDIA 驱动程序即可, **无需额外安装 cuda**，因为 PyTorch 不依赖本地的 nvcc 或完整的 CUDA Toolkit 来运行。

> [!abstract] [PyTorch 官方支持的 CUDA 版本](https://pytorch.org/get-started/locally/)
>  python 官方源当前为 12.4
> pytorch 源 stable版为 12.6； nightly 为 12.8

|  pip安装   |   版本  |
| --- | --- |
|   `pip3 install torch torchvision torchaudio`   |  CUDA12.4   |
|   `pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126`   |  CUDA12.6   |
| `pip3 install --pre torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/cu128` | CUDA12.8  |

## uv 安装
> 在 Python 生态中，`uv` 是一个新兴的工具，旨在提供高性能的包管理和任务运行功能。它由 `astral-sh`（以开发 `ruff` 等知名项目而闻名）创建，目标是成为 Python 开发者的一个快速、可靠且现代化的工具链组件。
> uv 默认使用二进制缓存，避免重复下载和编译相同的包。
我们按 uv 官网 命令脚本安装[^4]，会安装在个人用户目录下，uv 能很好遵循 xdg 规范
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```
## comfyui部署
### comfyui 源码下载
```
git clone https://github.com/comfyanonymous/ComfyUI.git
```
- comfyui 项目本身已经带 pyproject.toml，我们无需uv初始化`un init`
- 安装 Python 依赖，先 cd 到 ComfyUI 目录
	```
	uv add -r requirements.txt
	```
	
	![[uv-add-comfyui.png]]
> uv 自动创建了虚拟环境，安装好了依赖
- 测试 pytorch
	```
	uv run python -c "import torch;print(torch.cuda.is_available());print(torch.version.cuda)"
	```
	![[Pasted image 20250329195328.png]]
	> 版本 12.4，与我们前面[[linux 下使用 uv 部署 comfyui#PyTorch 自带 CUDA 是什么？]] 看到的一致
- 切换 pytorch 版本
	如果我们需要切换 pytorch 版本到 2.6，先卸载当前的
	```
	uv pip uninstall torch  torchvision torchaudio
	```
	> 因为torchvision、torchaudio 都与 torch 相关，我们一起卸载，避免不可控的混乱
	从 pytorch 源安装 pytorch2.6
	```
	uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126 
	```

[^1]: https://docs.nvidia.com/deploy/cuda-compatibility/

[^2]: https://wiki.archlinuxcn.org/wiki/NVIDIA

[^3]: https://nouveau.freedesktop.org/CodeNames.html#NV190

[^4]: https://docs.astral.sh/uv/getting-started/installation/
