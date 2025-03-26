---
title: 使用 uv 部署 传统的 python 项目
tags:
  - uv
  - python
description: 经常逛github，看到喜欢的开源 python 项目，老想部署玩玩，又偏爱 uv 来部署，记录个常规过程备忘
created: 2025-03-24 10:39
modified: 2025-03-26T12:26:03+08:00
draft: true
slug: uv-python
---
> 经常逛github，看到喜欢的开源 python 项目，老想部署玩玩，又偏爱 uv 来部署，记录个常规过程备忘

### 1. 初始化 `uv` 项目
如果项目没有 `pyproject.toml` 文件，通过 `uv init` 初始化一个基础的 `pyproject.toml` 文件。

```bash
uv init --bare
```

- `--bare` 选项会生成一个最简化的 `pyproject.toml`，不包含示例代码。
- 这会在当前目录下创建一个基本的 `pyproject.toml`，例如：
  ```toml
  [project]
  name = "my-project"
  version = "0.1.0"
  requires-python = ">=3.8"
  ```

### 2. 将 `requirements.txt` 中的依赖添加到 `pyproject.toml`
`uv` 提供了 `uv add` 命令，可以直接从 `requirements.txt` 文件中读取依赖并添加到 `pyproject.toml` 中。

运行以下命令：

```bash
uv add -r requirements.txt
```

- `-r` 表示从指定的 `requirements.txt` 文件中读取依赖。
- 这会将 `requirements.txt` 中的所有依赖添加到 `pyproject.toml` 的 `[project.dependencies]` 部分。例如，如果你的 `requirements.txt` 内容是：
  ```
  requests==2.28.1
  numpy>=1.21.0
  ```
  那么 `pyproject.toml` 会更新为：
  ```toml
  [project]
  name = "my-project"
  version = "0.1.0"
  requires-python = ">=3.8"
  dependencies = [
      "requests==2.28.1",
      "numpy>=1.21.0",
  ]
  ```

### 3. 创建虚拟环境并同步依赖
为了确保依赖正确安装，可以创建一个虚拟环境并同步这些依赖：

```bash
uv venv        # 创建虚拟环境，默认在 .venv 文件夹中
uv sync        # 根据 pyproject.toml 安装依赖到虚拟环境中
```
> 其实 uv add 的时候会自动创建 .venv 所以可省略 `uv venv`
- `uv sync` 会根据 `pyproject.toml` 和 `uv.lock`（如果存在）安装所有依赖到虚拟环境中。
- 如果你还没有 `uv.lock` 文件，`uv sync` 会自动生成一个，锁定具体的依赖版本。

### 4. 验证和清理
- 检查 `pyproject.toml` 是否正确包含了所有依赖。
- 如果确认无误，可以选择删除原来的 `requirements.txt` 文件，因为 `pyproject.toml` 已经成为依赖管理的“真相来源”。

### 注意事项
- **版本约束**：`uv add -r requirements.txt` 会保留 `requirements.txt` 中的版本约束（如 `==`、`>=` 等），直接迁移到 `pyproject.toml` 中。
- **手动调整**：如果你的 `requirements.txt` 中包含一些特殊的选项（例如 `-e` 可编辑安装或 `--index-url`），这些需要手动调整到 `pyproject.toml` 的 `[tool.uv]` 或 `[tool.uv.sources]` 部分。例如：
  ```toml
  [tool.uv.sources]
  my-package = { git = "https://github.com/user/repo.git" }
  ```
- **开发依赖**：如果 `requirements.txt` 中包含开发依赖（例如 `pytest`），可以用 `uv add --dev` 添加到 `[project.optional-dependencies]` 的 `dev` 组：
  ```bash
  uv add --dev pytest -r requirements.txt
  ```
  结果可能是：
  ```toml
  [project.optional-dependencies]
  dev = [
      "pytest>=7.0.0",
  ]
  ```

### 示例完整流程
假设你的 `requirements.txt` 是：

```bash
requests==2.28.1
numpy>=1.21.0
pytest>=7.0.0
```

执行以下命令：
```bash
uv init --bare
uv add -r requirements.txt
uv add --dev pytest  # 将 pytest 单独作为开发依赖
uv sync
```

最终 `pyproject.toml` 可能是：
```toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.8"
dependencies = [
    "requests==2.28.1",
    "numpy>=1.21.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
]
```

### 5. 生成 `requirements.txt`（可选）
如果你的生产环境仍然需要 `requirements.txt`，可以用以下命令生成：
```bash
uv pip compile pyproject.toml -o requirements.txt
```

这会根据 `pyproject.toml` 生成一个带有具体版本的 `requirements.txt`，供非 `uv` 环境使用。

通过以上步骤，你可以顺利将 `requirements.txt` 转换为 `uv` 的 `pyproject.toml`，并保持依赖同步。
