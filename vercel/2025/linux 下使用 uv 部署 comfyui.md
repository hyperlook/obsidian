---
title: Linux下 uv 快速部署 comfyui
tags:
  - linux
  - comfyui
  - uv
description: uv 是一个用 Rust 编写的 Python 包安装器和解析器。与传统的 pip 相比，它在速度和效率方面都有显著提升。对于 ComfyUI 这样依赖大量 Python 包的应用程序来说，使用 uv 可以显著缩短安装和依赖解析的时间，从而提高部署效率。
created: 2025-03-26 11:48
modified: 2025-03-29T22:38:28+08:00
draft: false
slug: comfyui-uv
---
> [!abstract] 为什么要使用 uv 来部署
>uv 是一个用 Rust 编写的 Python 包安装器和解析器。与传统的 pip 相比，它在速度和效率方面都有显著提升。对于 ComfyUI 这样依赖大量 Python 包的应用程序来说，使用 uv 可以显著缩短安装和依赖解析的时间，从而提高部署效率。
>
>comfyui 部署的易错的地方 在显卡 和 pytorch 的兼容性，我们先讲讲他们的来龙去脉 
## NVIDIA 显卡驱动

### Arch Linux 下显卡驱动安装[^2]
我用的是 Arch Linux，显卡是 4070s[^3]，查询命令
```bash
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
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
## comfyui部署
### comfyui 源码下载
```
git clone https://github.com/comfyanonymous/ComfyUI.git
```
- comfyui 项目本身已经带 pyproject.toml，我们无需uv初始化`un init`
### comfyui 依赖安装  
- 安装 Python 依赖，先 cd 到 ComfyUI 目录
	```python
	uv add -r requirements.txt
	```
	
	![[uv-add-comfyui.png]]
> uv 自动创建了虚拟环境，安装好了依赖
- 测试 pytorch
	```python
	uv run python -c "import torch;print(torch.cuda.is_available());print(torch.version.cuda)"
	```
	![[test-pytorch.png]]
	> 版本 12.4，与我们前面[[linux 下使用 uv 部署 comfyui#PyTorch 自带 CUDA 是什么？]] 看到的一致
- 切换 pytorch 版本
	如果我们需要切换 pytorch 版本到 2.6，先卸载当前的
	```bash
	uv pip uninstall torch  torchvision torchaudio
	```
	> 因为torchvision、torchaudio 都与 torch 相关，我们一起卸载，避免不可控的混乱
	从 pytorch 源安装 pytorch2.6
	```bash
	uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126 
	```
	
	![[install_pytorch_cuda26.png]]
	 可以看到 pytorch已经切换到带cuda12.6 的版本
	 > 这里我们并没有用 add 命令，因为额外的 --index-url，需要手动修改配置pyproject.toml，目前还有一点点依赖冲突问题，具体修改可参考[[linux 下使用 uv 部署 comfyui]]
### 测试运行comfyui
我们还是用 uv 来 run，这里我们始终没有激活虚拟环境，因为 uv run 的时候会自动调用前面创建的.venv 虚拟环境去跑。
```bash
uv run main.py --base-directory ../data --listen 0.0.0.0
```
![[uv_run_comfyui.png]]
> main.py 的具体参数，可以使用`--help`，也可以参考[[comfyui main.py 参数]]
> 上面我们使用了--base-directory 切换了数据目录，包括模型、自定义节点，输入输出以及自定义配置工作流等等，便于我们数据的保存和 comfyui 的升级切换。

## comfyui-manager 插件安装
> comfyui-manager 插件 是 comfyui 的重要插件，我们大部分情况下依赖它来安装其他插件，方便安装插件依赖和解决插件依赖冲突。
### 克隆ComfyUI-Manager 源码
克隆源码到 `custom_nodes`目录下，默认该目录在 comfyui 根目录下，因为我们这自定义了`--base-directory`，我们克隆到对应插件目录`~/data/custom_nodes`下
```
git clone https://github.com/ltdrdata/ComfyUI-Manager.git ~/data/custom_nodes/comfyui-manager
```
> 如果你之前已经有很多节点，重新升级了 comfyui，可以先把它们移到.dsabled目录下，插件目录下只保留comfyui-manager
![[comfyui-manager.png]]
### 配置 ComfyUI-Manager 使用 uv
- 因为我们使用 uv 部署，需要修改下comfyui-manager 配置，让它默认也使用 uv
	![[comfyui-manager-uv.png]]
- 这个配置在我们的用户文件目录下即`user/defualt/ComfyUI-Manager`下，修改`config.ini`加上`user_uv = True`
### 安装comfyui-manager 所需依赖
- cd 切换到 ComfyUI，因为我们的虚拟环境在这，我们后续操作都在这进行
- 拷贝comfyui-manager的 requirements.txt 为本目录下的 requirements-manager.txt
	```bash
	cp ~/data/custom_nodes/comfyui-manager/requirements.txt ./requirements-manager.txt
	```
- 虽然 uv pip 本身可以直接安装依赖，但ComfyUI-Manager 使用的 python -m uv pip 命令，我们必须补上 pip 依赖，它才能正常添加依赖。
	```
	echo pip >> requirements-manager.txt
	```
- 安装 ComfyUI-Manager 依赖
	```
	uv add -r requirements-manager.txt
	```
### 测试再次运行comfyui
![[ComfyUI-Manager-start.png]]
一切正常，没有报错，我们后续文章再来测试comfyui-manager 的插件安装

[^1]: https://docs.nvidia.com/deploy/cuda-compatibility/

[^2]: https://wiki.archlinuxcn.org/wiki/NVIDIA

[^3]: https://nouveau.freedesktop.org/CodeNames.html#NV190

[^4]: https://docs.astral.sh/uv/getting-started/installation/
