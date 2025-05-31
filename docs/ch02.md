# 2 FreeRTOS 内核分发包

## 2.1 简介

为帮助用户了解 FreeRTOS 内核的文件和目录结构，本章将：

- 提供 FreeRTOS 目录结构的顶层视图。
- 说明任何 FreeRTOS 工程所需的源文件。
- 介绍演示应用。
- 提供创建新 FreeRTOS 工程的信息。

本章描述仅针对官方 FreeRTOS 分发包。本书附带的示例采用略有不同的组织方式。

## 2.2 理解 FreeRTOS 分发包

### 2.2.1 定义：FreeRTOS 移植

FreeRTOS 可用大约二十种不同的编译器构建，并可运行在四十多种处理器架构上。每种编译器和处理器的组合称为一个 FreeRTOS 移植（port）。

### 2.2.2 构建 FreeRTOS

FreeRTOS 是一个为原本单线程裸机应用提供多任务能力的库。

FreeRTOS 以一组 C 源文件的形式提供。一些源文件适用于所有移植，另一些则特定于某个移植。将这些源文件作为工程的一部分进行构建，即可让应用使用 FreeRTOS API。每个官方 FreeRTOS 移植都配有一个可用作参考的演示应用。演示应用已预配置好所需的源文件和头文件。

每个演示应用在创建时都能“开箱即用”地无编译错误或警告。若后续工具链变更导致无法编译，请通过 FreeRTOS 支持论坛（<https://forums.FreeRTOS.org>）告知我们。第 2.3 节介绍了演示应用。

### 2.2.3 FreeRTOSConfig.h

FreeRTOSConfig.h 头文件中定义的常量用于配置内核。不要在源文件中直接包含 FreeRTOSConfig.h！应包含 FreeRTOS.h，由其在适当时机自动包含 FreeRTOSConfig.h。

FreeRTOSConfig.h 用于针对特定应用定制 FreeRTOS 内核。例如，FreeRTOSConfig.h 包含 `configUSE_PREEMPTION` 等常量，用于定义 FreeRTOS 采用协作式还是抢占式调度[^1]。

[^1]: 第 4.13 节介绍调度算法。

FreeRTOSConfig.h 应放在应用目录下，而不是 FreeRTOS 源码目录下。

主分发包中的每个演示应用都有自己的 FreeRTOSConfig.h 文件。建议以所用移植的演示应用中的 FreeRTOSConfig.h 为起点进行修改，而不是从零创建。

FreeRTOS 参考手册和 <https://www.freertos.org/a00110.html> 介绍了 FreeRTOSConfig.h 中出现的常量。并非所有常量都必须包含，许多常量省略时会采用默认值。

### 2.2.4 官方分发包

FreeRTOS 各个库（包括内核）可单独从各自的 Github 仓库或 zip 包下载。生产环境建议单独获取库，但初学建议下载主分发包，因其包含所有库和示例工程。

主分发包包含所有 FreeRTOS 库源码、全部内核移植和所有演示应用的工程文件。文件虽多，但实际应用只需极少一部分。

可通过 <https://github.com/FreeRTOS/FreeRTOS/releases/latest> 下载最新分发包 zip 文件，或用如下 Git 命令克隆主分发包（含各库子模块）：

```
git clone https://github.com/FreeRTOS/FreeRTOS.git --recurse-submodules
git clone git@github.com:FreeRTOS/FreeRTOS.git --recurse-submodules
```

图 2.1 显示了 FreeRTOS 分发包的第一层和第二层目录：

<a name="fig2.1" title="Figure 2.1 Top level directories within the FreeRTOS distribution"></a>

```
FreeRTOS
│ │
│ ├─Source  包含 FreeRTOS 内核源文件
│ │
│ └─Demo    包含预配置和特定于移植的 FreeRTOS 内核演示项目
│
FreeRTOS-Plus
│
├─Source    包含其他 FreeRTOS 和生态系统库的源代码
│
└─Demo      包含其他 FreeRTOS 和生态系统库的演示项目
```
***Figure 2.1*** *Top level directories within the FreeRTOS distribution*

分发包中仅包含一份 FreeRTOS 内核源文件；所有演示项目均期望在 FreeRTOS/Source 目录中找到内核源文件，若更改此目录结构，可能导致无法编译。

### 2.2.5 所有移植通用的 FreeRTOS 源文件

tasks.c 和 list.c 实现了 FreeRTOS 内核的核心功能，始终必需，位于 FreeRTOS/Source 目录下（见图 2.2）。同目录还包含以下可选源文件：

-  **queue.c**

   queue.c 提供队列和信号量服务（本书后续介绍），几乎总是必需。

-  **timers.c**

   timers.c 提供软件定时器功能，仅当应用使用软件定时器时才需构建。

-  **event_groups.c**

   event_groups.c 提供事件组功能，仅当应用使用事件组时才需构建。

-  **stream_buffer.c**

   stream_buffer.c 提供流缓冲区和消息缓冲区功能，仅当应用使用流缓冲区或消息缓冲区时才需构建。

-  **croutine.c**

   croutine.c 实现 FreeRTOS 协程功能，仅当应用使用协程时才需构建。协程主要用于极小型微控制器，现在很少使用，因此已不再维护，不建议新设计采用。本书不再介绍协程。

<a name="fig2.2" title="Figure 2.2 Core FreeRTOS source files within the FreeRTOS directory tree"></a>

```
FreeRTOS
│
└─Source
    │
    ├─tasks.c         FreeRTOS 源文件 - 始终必需
    ├─list.c          FreeRTOS 源文件 - 始终必需
    ├─queue.c         FreeRTOS 源文件 - 几乎总是必需
    ├─timers.c        FreeRTOS 源文件 - 可选
    ├─event_groups.c  FreeRTOS 源文件 - 可选
    ├─stream_buffer.c FreeRTOS 源文件 - 可选
    └─croutine.c      FreeRTOS 源文件 - 可选且不再维护
```
***Figure 2.2*** *Core FreeRTOS source files within the FreeRTOS directory tree*

我们认识到，zip 文件分发中的文件名可能会导致命名空间冲突，因为许多项目可能已经使用了相同名称的文件。用户可以根据需要更改文件名，但分发中的名称不能更改，因为这样做会破坏与现有用户项目以及 FreeRTOS 感知的开发工具的兼容性。

### 2.2.6 针对特定移植的 FreeRTOS 源文件

FreeRTOS/Source/portable 目录包含特定于 FreeRTOS 移植的源文件。该目录按编译器、处理器架构分层组织，如图 2.3 所示。

要在某架构（*architecture*）和编译器（*compiler*）上运行 FreeRTOS，除核心源文件外，还需构建 FreeRTOS/Source/portable/[compiler]/[architecture] 目录下的文件。

如第 3 章“堆内存管理”所述，FreeRTOS 也将堆内存分配视为可移植层的一部分。如果 `configSUPPORT_DYNAMIC_ALLOCATION` 设为 0，则无需在工程中包含堆内存分配方案。

FreeRTOS 在 FreeRTOS/Source/portable/MemMang 目录下提供了示例堆分配方案。如果配置为使用动态内存分配，需在工程中包含该目录下的一个堆实现源文件，或自行实现。

> **注意：工程中只能包含一个示例堆分配实现。**

<a name="fig2.3" title="Figure 2.3 Port specific source files within the FreeRTOS directory tree"></a>

```
FreeRTOS
│
└─Source
    │
    └─portable  包含所有特定于移植的源文件的目录
        │
        ├─MemMang  包含替代堆分配源文件的目录
        │
        ├─[compiler 1]  包含特定于编译器 1 的移植文件的目录
        │   │
        │   ├─[architecture 1]  包含编译器 1 架构 1 移植的文件
        │   ├─[architecture 2]  包含编译器 1 架构 2 移植的文件
        │   └─[architecture 3]  包含编译器 1 架构 3 移植的文件
        │
        └─[compiler 2]  包含特定于编译器 2 的移植文件的目录
            │
            ├─[architecture 1]  包含编译器 2 架构 1 移植的文件
            ├─[architecture 2]  包含编译器 2 架构 2 移植的文件
            └─[etc.]
```
***Figure 2.3*** *Port specific source files within the FreeRTOS directory tree*

### 2.2.7 头文件包含路径

FreeRTOS 需要在编译器的包含路径中添加三个目录：

1. FreeRTOS 内核头文件路径：FreeRTOS/Source/include
2. 所用移植的特定源文件路径：FreeRTOS/Source/portable/[compiler]/[architecture]
3. 正确的 FreeRTOSConfig.h 头文件所在路径

### 2.2.8 头文件

使用 FreeRTOS API 的源文件必须先包含 FreeRTOS.h，再包含对应 API 的头文件，如 task.h、queue.h、semphr.h、timers.h、event_groups.h、stream_buffer.h、message_buffer.h 或 croutine.h。不要显式包含其他 FreeRTOS 头文件——FreeRTOS.h 会自动包含 FreeRTOSConfig.h。

## 2.3 演示应用

每个 FreeRTOS 移植都至少配有一个演示应用，在创建时可“开箱即用”无编译错误或警告。若后续工具链变更导致无法编译，请通过 FreeRTOS 支持论坛（<https://forums.FreeRTOS.org>）告知我们。

> **跨平台支持**：FreeRTOS 在 Windows、Linux 和 MacOS 及多种工具链（嵌入式和传统）下开发和测试。但由于版本差异或遗漏测试，偶尔会出现构建错误。请通过 FreeRTOS 支持论坛反馈。

演示应用的主要目的：

- 提供包含正确文件和编译选项的工作示例工程。
- 便于“开箱即用”实验，无需复杂配置或先验知识。
- 演示 FreeRTOS API 的用法。
- 可作为实际应用的基础。
- 用于压力测试内核实现。

每个演示工程位于 FreeRTOS/Demo 目录下唯一的子目录中，子目录名表明其对应的移植。

FreeRTOS.org 网站为每个演示应用提供页面，内容包括：
- 如何在 FreeRTOS 目录结构中定位工程文件
- 工程配置使用的硬件或仿真器
- 如何搭建硬件运行演示
- 如何构建演示
- 预期行为

所有演示工程都会创建一部分“通用演示任务”，其实现位于 FreeRTOS/Demo/Common/Minimal 目录。通用演示任务用于演示 API 用法和测试内核移植，不具备实际功能。

许多演示工程还可配置为创建简单的“blinky”入门项目，通常包含两个 RTOS 任务和一个队列。

每个演示工程都包含 main.c 文件，内含 main() 函数，用于创建演示任务并启动内核。具体信息见各 main.c 文件注释。

<a name="fig2.4" title="Figure 2.4 The demo directory hierarchy"></a>

```
FreeRTOS
    │
    └─Demo          包含所有演示项目的目录
        │
        ├─[Demo x]  包含构建演示 'x' 的项目文件
        ├─[Demo y]  包含构建演示 'y' 的项目文件
        ├─[Demo z]  包含构建演示 'z' 的项目文件
        └─Common    包含所有演示应用共用的文件
```
***Figure 2.4*** *The demo directory hierarchy*

## 2.4 创建 FreeRTOS 工程

### 2.4.1 适配提供的演示工程

每个 FreeRTOS 移植都至少配有一个预配置的演示应用。建议通过适配这些现有工程来创建新项目，以确保包含正确的文件、安装正确的中断处理程序并设置正确的编译选项。

新建应用的步骤如下：

1. 打开提供的演示工程，确保其能正常构建和运行。
2. 删除实现演示任务的源文件（位于 Demo/Common 目录）。
3. 删除 main() 中除 `prvSetupHardware()` 和 `vTaskStartScheduler()` 外的所有函数调用，参考代码见下。
4. 确认工程仍能正常构建。

这样即可得到一个包含正确 FreeRTOS 源文件但无具体功能的工程。

```c
int main( void )
{
    /* 执行必要的硬件初始化。 */
    prvSetupHardware();

    /* --- 可在此创建应用任务 --- */

    /* 启动已创建的任务。 */
    vTaskStartScheduler();

    /* 仅当堆空间不足无法启动调度器时才会执行到此。 */
    for( ;; );
    return 0;
}
```
***代码清单 2.1*** *新 main() 函数模板*

### 2.4.2 从零创建新工程

如前所述，建议基于现有演示工程创建新项目。如果确需从零开始，可按如下流程：

1. 用所选工具链新建一个尚未包含任何 FreeRTOS 源文件的工程。
2. 确保新工程能正常构建、下载到目标硬件并运行。
3. 确认已有可用工程后，再添加表 1 所列的 FreeRTOS 源文件。
4. 复制所用移植演示工程中的 `FreeRTOSConfig.h` 文件到新工程目录。
5. 将以下目录添加到头文件搜索路径：
   - FreeRTOS/Source/include
   - FreeRTOS/Source/portable/[compiler]/[architecture]
   - `FreeRTOSConfig.h` 所在目录
6. 复制相关演示工程的编译器设置。
7. 安装所需的 FreeRTOS 中断处理程序。可参考所用移植和演示工程的网页说明。

| 文件                            | 位置                     |
|---------------------------------|--------------------------|
| tasks.c                         | FreeRTOS/Source          |
| queue.c                         | FreeRTOS/Source          |
| list.c                          | FreeRTOS/Source          |
| timers.c                        | FreeRTOS/Source          |
| event_groups.c                  | FreeRTOS/Source          |
| stream_buffer.c                 | FreeRTOS/Source          |
| 所有 C/汇编文件                 | FreeRTOS/Source/portable/[compiler]/[architecture] |
| heap_n.c                        | FreeRTOS/Source/portable/MemMang，n为1~5之一 |

***表 1*** *工程需包含的 FreeRTOS 源文件*

**关于堆内存的说明：**
如果 `configSUPPORT_DYNAMIC_ALLOCATION` 为 0，则无需包含堆内存分配方案。否则需包含一个堆分配实现（heap_n.c 或自定义）。详见第 3 章。

## 2.5 数据类型和编码风格指南

### 2.5.1 数据类型

每个 FreeRTOS 移植都有独特的 portmacro.h 头文件，定义了两个与移植相关的数据类型：`TickType_t` 和 `BaseType_t`。说明如下：

- `TickType_t`

  FreeRTOS 配置了一个周期性中断，称为 tick 中断。

  自应用启动以来发生的 tick 中断次数称为 *tick 计数*，用于衡量时间。

  两次 tick 中断之间的时间称为 *tick 周期*。时间以 tick 周期的倍数指定。

  `TickType_t` 用于保存 tick 计数值和指定时间。

  `TickType_t` 可为无符号 16/32/64 位类型，取决于 FreeRTOSConfig.h 中 `configTICK_TYPE_WIDTH_IN_BITS` 的设置。该设置与架构相关，FreeRTOS 移植会检查其有效性。

  8/16 位架构用 16 位类型可提升效率，但会严重限制最大可指定的阻塞时间。32/64 位架构无须用 16 位类型。

  以往的 `configUSE_16_BIT_TICKS` 已被 `configTICK_TYPE_WIDTH_IN_BITS` 替代。新设计应使用后者。

   | configTICK_TYPE_WIDTH_IN_BITS | 8位架构 | 16位架构 | 32位架构 | 64位架构 |
   | --- | --- | --- | --- | --- |
   | TICK_TYPE_WIDTH_16_BITS | uint16_t | uint16_t | uint16_t | N/A |
   | TICK_TYPE_WIDTH_32_BITS | uint32_t | uint32_t | uint32_t | N/A |
   | TICK_TYPE_WIDTH_64_BITS | N/A | N/A | uint64_t | uint64_t |

***表 2*** *TickType_t 数据类型与 configTICK_TYPE_WIDTH_IN_BITS 配置*

- `BaseType_t`

  始终定义为该架构最高效的数据类型。64 位架构用 64 位类型，32 位架构用 32 位类型，16 位架构用 16 位类型，8 位架构用 8 位类型。

  `BaseType_t` 通常用于只取有限值的返回类型，以及 `pdTRUE`/`pdFALSE` 类型的布尔值。

### 2.5.2 变量命名

变量名前缀表示类型：`c` 表示 char，`s` 表示 int16_t（short），`l` 表示 int32_t（long），`x` 表示 BaseType_t 及其他非标准类型（结构体、任务句柄、队列句柄等）。

无符号变量加前缀 `u`，指针变量加前缀 `p`。如 uint8_t 类型变量前缀为 `uc`，char* 类型变量前缀为 `pc`。

### 2.5.3 函数命名

函数名前缀包含返回类型和定义所在文件。例如：
- v**Task**PrioritySet() 返回 void，定义于 tasks.c。
- x**Queue**Receive() 返回 BaseType_t，定义于 queue.c。
- pv**Timer**GetTimerID() 返回 void*，定义于 timers.c。

文件作用域（私有）函数前缀为 `prv`。

### 2.5.4 格式化

部分演示应用使用制表符（tab），每个 tab 等于四个空格。内核源码现已不再使用 tab。

### 2.5.5 宏命名

大多数宏用大写字母，前缀表示定义位置。见下表：

| 前缀 | 宏定义位置 |
|------|------------|
| port（如 portMAX_DELAY） | portable.h 或 portmacro.h |
| task（如 taskENTER_CRITICAL()） | task.h |
| pd（如 pdTRUE） | projdefs.h |
| config（如 configUSE_PREEMPTION） | FreeRTOSConfig.h |
| err（如 errQUEUE_FULL） | projdefs.h |

***表 3*** *宏名前缀*

注意，信号量 API 主要用宏实现，但命名风格与函数一致。

下表为 FreeRTOS 源码常用宏：

| 宏        | 值 |
|-----------|----|
| pdTRUE    | 1  |
| pdFALSE   | 0  |
| pdPASS    | 1  |
| pdFAIL    | 0  |

***表 4*** *常用宏定义*

### 2.5.6 关于类型强制转换的说明

FreeRTOS 源码需兼容多种编译器，不同编译器对类型转换的警告处理方式不同。因此源码中类型转换较多。
