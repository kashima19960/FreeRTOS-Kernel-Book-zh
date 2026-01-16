# FreeRTOS 内核书籍中文翻译版

[![ci](https://github.com/kashima19960/FreeRTOS-Kernel-Book-zh/workflows/ci/badge.svg)](https://kashima19960.github.io/FreeRTOS-Kernel-Book-zh/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

本仓库包含 [FreeRTOS-Kernel-Book](https://github.com/FreeRTOS/FreeRTOS-Kernel-Book) 一书的中文翻译内容。翻译工作使用了GPT-5.2-Codex，采用mkdocs-material 主题构建在线文档。

## 📖 关于本书

《精通 FreeRTOS™ 实时内核 - 实践教程指南》是学习 FreeRTOS 实时操作系统内核的权威指南。本书详细介绍了 FreeRTOS 的核心概念、API 使用方法以及实际应用案例，是嵌入式系统开发者的必备参考资料。

- **作者**: Richard Barry 及 FreeRTOS 团队
- **版本**: 1.0
- **语言**: 中文（原版英文）

## 🚀 在线阅读

📚 **[在线阅读地址](https://kashima19960.github.io/FreeRTOS-Kernel-Book-zh/)**

## 📋 目录结构

### 主要章节

1. **[前言](docs/ch01-前言.md)** - FreeRTOS 简介与小型嵌入式系统中的多任务处理
2. **[FreeRTOS 内核分发包](docs/ch02-FreeRTOS%20内核分发包.md)** - 理解 FreeRTOS 的分发结构和项目创建
3. **[堆内存管理](docs/ch03-堆内存管理.md)** - FreeRTOS 的内存管理机制
4. **[任务管理](docs/ch04-任务管理.md)** - 任务的创建、调度和状态管理
5. **[队列管理](docs/ch05-队列管理.md)** - 队列的使用和任务间通信
6. **[软件定时器管理](docs/ch06-软件定时器管理.md)** - 软件定时器的创建和使用
7. **[中断管理](docs/ch07-中断管理.md)** - 中断处理和与任务的同步
8. **[资源管理](docs/ch08-资源管理.md)** - 互斥锁和信号量的使用
9. **[事件组](docs/ch09-事件组.md)** - 事件组的同步机制
10. **[任务通知](docs/ch10-任务通知.md)** - 轻量级的任务通知机制
11. **[低功耗支持](docs/ch11-低功耗支持.md)** - 节能和空闲任务管理
12. **[开发者支持](docs/ch12-开发者支持.md)** - 调试和开发工具
13. **[故障排查](docs/ch13-故障排查.md)** - 常见问题诊断和解决

### 目录文件

- **英文目录**: [Table of Contents](toc.md)
- **中文目录**: [目录](docs/目录.md)

## 📁 项目结构

```
FreeRTOS-Kernel-Book-zh/
├── README.md              # 项目说明文档
├── mkdocs.yml            # MkDocs 配置文件
├── toc.md                # 英文目录
├── ch01.md - ch13.md     # 英文版章节（原版）
├── docs/                 # 中文翻译文档
│   ├── 目录.md           # 中文目录
│   ├── 书名.md           # 中文书名页
│   ├── 缩略语表.md       # 缩略语对照表
│   └── ch01-前言.md      # 中文版各章节
│       ...
├── examples/             # 书中示例代码
│   ├── Win32-simulator-MSVC/  # Windows Visual Studio 示例项目
│   └── uncrustify.cfg    # 代码格式化配置
├── media/               # 图片和媒体文件
│   ├── freeRTOS.png     # FreeRTOS 标志
│   ├── figure_*.png     # 书中图表
│   └── aws-logo*.png    # AWS 相关标志
├── booktitle.md         # 英文书名页
├── copyright.md         # 版权信息
├── dedication.md        # 致谢页
├── abbreviations.md     # 英文缩略语表
├── CONTRIBUTING.md      # 贡献指南
└── SECURITY.md         # 安全政策
```

## 🛠️ 本地构建

本项目使用 [MkDocs](https://www.mkdocs.org/) 和 [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) 主题构建在线文档。

### 环境要求

- Python 3.7+
- pip

### 安装步骤

1. **克隆仓库**
   ```bash
   git clone https://github.com/kashima19960/FreeRTOS-Kernel-Book-zh.git
   cd FreeRTOS-Kernel-Book-zh
   ```

2. **创建虚拟环境**（推荐）
   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # Linux/macOS
   source .venv/bin/activate
   ```

3. **安装依赖**
   ```bash
   pip install mkdocs-material
   ```

4. **本地预览**
   ```bash
   mkdocs serve
   ```
   然后访问 http://127.0.0.1:8000

5. **构建静态文件**
   ```bash
   mkdocs build
   ```

## 🔧 示例代码

书中的所有示例代码位于 `examples/` 目录下，包含：

- **Win32 模拟器项目**: 可在 Windows 上使用 Visual Studio 编译运行
- **完整的 FreeRTOS 配置示例**: 展示了各种配置选项的使用
- **代码风格配置**: uncrustify.cfg 用于代码格式化


## 🔗 相关链接

- **FreeRTOS 官网**: https://www.freertos.org
- **FreeRTOS GitHub**: https://github.com/FreeRTOS
- **原版书籍下载**: https://www.freertos.org/Documentation/code
- **FreeRTOS 文档**: https://freertos.org/features.html

## ⭐ 致谢

- 感谢 Richard Barry 和 FreeRTOS 团队创作了这本优秀的教程
- 感谢 Amazon Web Services 对 FreeRTOS 项目的支持
