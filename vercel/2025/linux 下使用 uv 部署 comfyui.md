---
title: Linux下uv快速部署 comfyui
tags:
  - linux
  - comfyui
description: 
created: 2025-03-26 11:48
modified: 2025-03-29T16:24:16+08:00
draft: true
slug: comfyui-uv
---

## 显卡驱动安装
这一步是必需步骤，这个不是本文重点，略过不提

## CUDA 和 PyTorch
### CUDA Toolkit
CUDA Toolkit 是套完整的开发工具包，包括编译器（nvcc）、库（如 cuBLAS、cuFFT）和运行时（CUDA Runtime）。通过CUDA，开发者可以使用NVIDIA提供的CUDA API来编写程序，从而在支持CUDA的GPU上执行计算密集型任务，比如物理模拟、图像和视频处理、机器学习算法等。


### PyTorch 自带 CUDA 是什么？

PyTorch 的官方二进制发行版内置了一个精简的 CUDA Runtime。用于支持 PyTorch 的 GPU 计算。因为 comfyui 的依赖 pytorch 默认使用的是最新版，预编译的 PyTorch 包已经包含了运行时所需的 CUDA 库（如 cuDNN、cuBLAS 等），因此只需要确保你的系统上安装了正确的 NVIDIA 驱动程序即可, **无需额外安装 cuda**，省去了需要配套兼容的 cuda 烦恼。你不需要单独安装 CUDA Toolkit，因为 PyTorch 不依赖本地的 nvcc 或完整的 CUDA Toolkit 来运行。

> [!abstract] [PyTorch 官方支持的 CUDA 版本](https://pytorch.org/get-started/locally/)
> 可以看到 python 官方的软件版仓库默认是 12.4
> 新版12.6 需要从 pytorch 源获取

|  pip安装   |   版本  |
| --- | --- |
|   `pip3 install torch torchvision torchaudio`   |  CUDA12.4   |
|   `pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126`   |  CUDA12.6   |


## uv 安装
> 在 Python 生态中，`uv` 是一个新兴的工具，旨在提供高性能的包管理和任务运行功能。它由 `astral-sh`（以开发 `ruff` 等知名项目而闻名）创建，目标是成为 Python 开发者的一个快速、可靠且现代化的工具链组件。
> uv 默认使用二进制缓存，避免重复下载和编译相同的包。
> 
```
ffff
```
- aa
- bbb
- cc[^1]

[^1]: [[打造 舒服的 shell - zsh]]
