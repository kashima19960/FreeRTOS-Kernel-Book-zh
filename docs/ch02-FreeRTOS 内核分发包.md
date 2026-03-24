# 2 FreeRTOS 内核分发包

## 2.1 简介

为帮助用户快速熟悉 FreeRTOS 内核的文件与目录结构，本章将：

- 从顶层视角介绍 FreeRTOS 目录结构。
- 说明任意 FreeRTOS 工程所需的源文件。
- 介绍演示应用（Demo）。
- 给出创建新 FreeRTOS 工程的方法。

本章描述仅针对官方 FreeRTOS 分发包。本书配套示例采用了略有不同的组织方式。


## 2.2 理解 FreeRTOS 分发包

### 2.2.1 定义：FreeRTOS Port（移植）

FreeRTOS 可使用约二十种不同编译器构建，并可运行在四十多种处理器架构上。每一种“编译器 + 处理器”的受支持组合都称为一个 FreeRTOS port（移植）。


### 2.2.2 构建 FreeRTOS

FreeRTOS 是一个库，它为原本单线程、裸机（bare-metal）应用提供多任务能力。

FreeRTOS 以一组 C 源文件形式提供。其中一部分源文件对所有 port 通用，另一部分则与具体 port 相关。将这些源文件编入你的工程后，应用即可使用 FreeRTOS API。每个官方 FreeRTOS port 都提供了可作为参考的 demo 应用；demo 工程已预配置为构建正确的源文件并包含正确的头文件。

在 demo 创建时，均可“开箱即用”构建，且无编译错误和警告。如果后续构建工具变化导致不再满足该条件，请通过 FreeRTOS 支持论坛（<https://forums.FreeRTOS.org>）告知我们。第 2.3 节将介绍 demo 应用。


### 2.2.3 FreeRTOSConfig.h

内核通过名为 `FreeRTOSConfig.h` 的头文件中定义的常量进行配置。不要在你的源文件中直接包含 `FreeRTOSConfig.h`！应包含 `FreeRTOS.h`，后者会在恰当时机包含 `FreeRTOSConfig.h`。

`FreeRTOSConfig.h` 用于针对特定应用裁剪 FreeRTOS 内核。例如，文件中的 `configUSE_PREEMPTION` 常量定义了 FreeRTOS 使用协作式调度还是抢占式调度[^1]。

[^1]: 第 4.13 节介绍调度算法。

`FreeRTOSConfig.h` 是面向具体应用的配置文件，因此应放在应用目录中，而不是放在 FreeRTOS 源码目录中。

官方主分发包为每个 FreeRTOS port 都提供一个 demo 应用，而每个 demo 都有自己的 `FreeRTOSConfig.h`。建议以你所用 port 的 demo 中提供的 `FreeRTOSConfig.h` 为起点进行修改，而不是从零新建。

FreeRTOS 参考手册和 <https://www.freertos.org/a00110.html> 描述了 `FreeRTOSConfig.h` 中可用的配置项。无需把所有常量都写进 `FreeRTOSConfig.h`——许多配置在缺省时会使用默认值。


### 2.2.4 官方分发方式

FreeRTOS 的各个库（包括内核）既可从各自 GitHub 仓库获取，也可下载 zip 压缩包。单独获取库在生产项目中很方便；但若你刚开始接触 FreeRTOS，更建议先下载主分发包，因为它同时包含库和示例工程。

主分发包包含全部 FreeRTOS 库源码、全部内核 port，以及全部 demo 应用工程文件。不要被文件数量吓到！实际应用只需其中很小一部分。

可通过 <https://github.com/FreeRTOS/FreeRTOS/releases/latest> 下载包含最新分发内容的 zip 文件。也可以使用下面任一 Git 命令克隆主仓库，并递归拉取各子模块库：

* * *
```
git clone https://github.com/FreeRTOS/FreeRTOS.git --recurse-submodules

git clone git@github.com:FreeRTOS/FreeRTOS.git --recurse-submodules
```
* * *


图 2.1 展示了 FreeRTOS 分发包的一级、二级目录：

<a name="fig2.1" title="Figure 2.1 Top level directories within the FreeRTOS distribution"></a>

* * *
```
FreeRTOS
│ │
│ ├─Source  Contains the FreeRTOS kernel source files
│ │
│ └─Demo    Contains pre-configured and port specific FreeRTOS kernel demo projects
│
FreeRTOS-Plus
│
├─Source    Contains source code for other FreeRTOS and ecosystem libraries
│
└─Demo      Contains demo projects for other FreeRTOS and ecosystem libraries
```
***图 2.1*** *FreeRTOS 分发包中的顶层目录*
* * *

分发包中只包含一份 FreeRTOS 内核源码；所有 demo 工程都假定内核源码位于 `FreeRTOS/Source` 目录，如果变更目录结构，工程可能无法构建。


### 2.2.5 所有 Port 通用的 FreeRTOS 源文件

`tasks.c` 和 `list.c` 实现了 FreeRTOS 内核核心功能，始终必需。它们位于 `FreeRTOS/Source` 目录下，如图 2.2 所示。同目录还包含以下可选源文件：

-  **queue.c**

   `queue.c` 同时提供队列与信号量服务（后续章节详述），几乎总是需要。

-  **timers.c**

   `timers.c` 提供软件定时器功能（后续章节详述）。仅当应用使用软件定时器时才需要构建。

-  **event_groups.c**

   `event_groups.c` 提供事件组功能（后续章节详述）。仅当应用使用事件组时才需要构建。

-  **stream_buffer.c**

   `stream_buffer.c` 同时提供流缓冲区和消息缓冲区功能（后续章节详述）。仅当应用使用流缓冲区或消息缓冲区时才需要构建。

-  **croutine.c**

   `croutine.c` 实现 FreeRTOS 协程功能。仅当应用使用协程时才需要构建。协程主要面向超小型 MCU，如今较少使用，因此已不再维护，不建议新设计继续采用。本书也不介绍协程。


<a name="fig2.2" title="Figure 2.2 Core FreeRTOS source files within the FreeRTOS directory tree"></a>

* * *
```
FreeRTOS
│
└─Source
    │
    ├─tasks.c         FreeRTOS source file - always required
    ├─list.c          FreeRTOS source file - always required
    ├─queue.c         FreeRTOS source file - nearly always required
    ├─timers.c        FreeRTOS source file - optional
    ├─event_groups.c  FreeRTOS source file – optional
    ├─stream_buffer.c FreeRTOS source file - optional
    └─croutine.c      FreeRTOS source file – optional and no longer maintained
```
***图 2.2*** *FreeRTOS 目录树中的核心源文件*
* * *

我们也注意到 zip 分发包中的文件名可能与用户项目发生命名冲突，因为很多项目已存在同名文件。用户可按需改名，但官方分发包中的文件名不能修改，否则会破坏与既有用户工程以及 FreeRTOS 感知型开发工具的兼容性。


### 2.2.6 与 Port 相关的 FreeRTOS 源文件

`FreeRTOS/Source/portable` 目录包含与具体 FreeRTOS port 相关的源文件。该目录按层级组织：先按编译器，再按处理器架构。图 2.3 给出了层级结构。

若要在架构“*architecture*”的处理器上，使用编译器“*compiler*”运行 FreeRTOS，除核心源文件外，还必须构建 `FreeRTOS/Source/portable/[compiler]/[architecture]` 目录中的文件。

如第 3 章“堆内存管理”所述，FreeRTOS 将堆内存分配也视为可移植层的一部分。若 `configSUPPORT_DYNAMIC_ALLOCATION` 设为 0，则不要在工程中包含任何堆内存分配方案。

FreeRTOS 在 `FreeRTOS/Source/portable/MemMang` 目录中提供了示例堆分配方案。如果 FreeRTOS 配置为使用动态内存分配，则必须在工程中包含该目录中的某个堆实现文件，或自行提供实现。

> **! 不要在同一个工程中同时包含多个示例堆分配实现。**


<a name="fig2.3" title="Figure 2.3 Port specific source files within the FreeRTOS directory tree"></a>

* * *
```
FreeRTOS
│
└─Source
    │
    └─portable Directory containing all port specific source files
        │
        ├─MemMang Directory containing the alternative heap allocation source files
        │
        ├─[compiler 1] Directory containing port files specific to compiler 1
        │   │
        │   ├─[architecture 1] Contains files for the compiler 1 architecture 1 port
        │   ├─[architecture 2] Contains files for the compiler 1 architecture 2 port
        │   └─[architecture 3] Contains files for the compiler 1 architecture 3 port
        │
        └─[compiler 2] Directory containing port files specific to compiler 2
            │
            ├─[architecture 1] Contains files for the compiler 2 architecture 1 port
            ├─[architecture 2] Contains files for the compiler 2 architecture 2 port
            └─[etc.]
```
***图 2.3*** *FreeRTOS 目录树中的 port 专属源文件*
* * *

### 2.2.7 头文件包含路径

编译器的 include path 需要包含三个目录：

1. FreeRTOS 内核公共头文件路径：`FreeRTOS/Source/include`。

2. 当前所用 port 的专属头文件路径：`FreeRTOS/Source/portable/[compiler]/[architecture]`。

3. 正确的 `FreeRTOSConfig.h` 所在路径。


### 2.2.8 头文件

使用 FreeRTOS API 的源文件必须先包含 `FreeRTOS.h`，再包含对应 API 原型所在头文件（`task.h`、`queue.h`、`semphr.h`、`timers.h`、`event_groups.h`、`stream_buffer.h`、`message_buffer.h` 或 `croutine.h`）。不要显式包含其他 FreeRTOS 头文件——`FreeRTOS.h` 会自动包含 `FreeRTOSConfig.h`。


## 2.3 Demo 应用

每个 FreeRTOS port 至少带有一个 demo 应用，在创建时可“开箱即用”构建且无编译错误或警告。如果后续构建工具变化导致不再如此，请通过 FreeRTOS 支持论坛（<https://forums.FreeRTOS.org>）反馈。

> **跨平台支持**：FreeRTOS 在 Windows、Linux、MacOS 以及多种工具链（含嵌入式工具链与传统工具链）上开发和测试。但由于版本差异或测试遗漏，偶尔仍可能出现构建错误。若发现此类问题，请在 FreeRTOS 支持论坛（<https://forums.FreeRTOS.org>）告知我们。

Demo 应用的目的包括：

- 提供可工作的预配置工程示例（包含正确文件、正确编译选项）。
- 让用户在最小化配置和先验知识前提下“开箱即试”。
- 演示 FreeRTOS API 的用法。
- 作为构建真实应用的基础。
- 对内核实现进行压力测试。

每个 demo 工程都位于 `FreeRTOS/Demo` 下的独立子目录中。子目录名指明该 demo 对应的 port。

FreeRTOS.org 网站为每个 demo 应用提供独立页面，页面说明包括：

- 在 FreeRTOS 目录结构中如何找到该 demo 的工程文件。
- 工程所配置使用的硬件或仿真器。
- 运行 demo 的硬件配置方法。
- demo 的构建方法。
- demo 的预期行为。

所有 demo 工程都会创建一部分“通用 demo 任务（common demo tasks）”，其实现位于 `FreeRTOS/Demo/Common/Minimal` 目录。这些任务用于演示 FreeRTOS API 和测试 FreeRTOS port，并不实现特定业务功能。

许多 demo 工程还能配置为创建一个简单的“blinky”入门项目，通常会创建两个 RTOS 任务和一个队列。

每个 demo 工程都包含 `main.c`，其中的 `main()` 函数会在启动 FreeRTOS 内核前创建 demo 应用任务。具体 demo 的细节请查看各自 `main.c` 内注释。

<a name="fig2.4" title="Figure 2.4 The demo directory hierarchy"></a>

* * *
```
FreeRTOS
    │
    └─Demo          Directory containing all the demo projects
        │
        ├─[Demo x]  Contains the project file that builds demo 'x'
        ├─[Demo y]  Contains the project file that builds demo 'y'
        ├─[Demo z]  Contains the project file that builds demo 'z'
        └─Common    Contains files that are built by all the demo applications
```
***图 2.4*** *Demo 目录层级*
* * *


## 2.4 创建 FreeRTOS 工程

### 2.4.1 由现有 Demo 工程改造

每个 FreeRTOS port 至少提供一个预配置 demo。建议基于这些现有工程创建新项目，以确保：包含了正确的文件、安装了正确的中断处理函数、设置了正确的编译选项。

从现有 demo 创建新应用的步骤：

1. 打开提供的 demo 工程，确认其能按预期构建并运行。

2. 移除实现 demo 任务的源文件，即 `Demo/Common` 目录下的文件。

3. 删除 `main()` 中除 `prvSetupHardware()` 与 `vTaskStartScheduler()` 外的所有函数调用，如清单 2.1 所示。

4. 验证工程仍可构建。

按上述步骤可得到一个“已包含正确 FreeRTOS 源文件，但尚未实现业务功能”的工程骨架。


<a name="list2.1" title="Listing 2.1 The template for a new main() function"></a>


```c
int main( void )
{
    /* Perform any hardware setup necessary. */
    prvSetupHardware();

    /* --- APPLICATION TASKS CAN BE CREATED HERE --- */

    /* Start the created tasks running. */
    vTaskStartScheduler();

    /* Execution will only reach here if there was insufficient heap to
       start the scheduler. */
    for( ;; );
    return 0;
}
```
***清单 2.1*** *新 `main()` 函数模板*



### 2.4.2 从零创建新工程

如前所述，推荐从现有 demo 工程创建新项目。若不希望这样做，可按以下流程从零创建：

1. 使用所选工具链创建一个尚未包含任何 FreeRTOS 源文件的新工程。

1. 确认该新工程能够构建、下载到目标硬件并执行。

1. 仅在确认你已有可工作的基础工程后，再把表 1 所列 FreeRTOS 源文件加入工程。

1. 将当前 port 的 demo 工程所用 `FreeRTOSConfig.h` 复制到你的新工程目录。

1. 将以下目录加入工程的头文件搜索路径：

   - `FreeRTOS/Source/include`
   - `FreeRTOS/Source/portable/[compiler]/[architecture]`（其中 `[compiler]` 与 `[architecture]` 必须与所选 port 匹配）
   - 存放 `FreeRTOSConfig.h` 的目录

1. 从对应 demo 工程复制编译器设置。

1. 安装可能需要的 FreeRTOS 中断处理函数。请参考所用 port 对应网页与配套 demo 工程。

<a name="tbl1" title="Table 1 FreeRTOS source files to include in the project"></a>

* * *
| 文件 | 位置 |
|---------------------------------|------------------------------|
| tasks.c                         | FreeRTOS/Source              |
| queue.c                         | FreeRTOS/Source              |
| list.c                          | FreeRTOS/Source              |
| timers.c                        | FreeRTOS/Source              |
| event\_groups.c                 | FreeRTOS/Source              |
| stream\_buffer.c                | FreeRTOS/Source              |
| 所有 C 和汇编文件                | FreeRTOS/Source/portable/[compiler]/[architecture] |
| heap\_n.c                       | FreeRTOS/Source/portable/MemMang，其中 n 为 1、2、3、4 或 5 |

***表 1*** *工程中应包含的 FreeRTOS 源文件*
* * *

**关于堆内存的说明：**
如果 `configSUPPORT_DYNAMIC_ALLOCATION` 为 0，则不要在工程中包含堆内存分配方案。否则必须包含一种堆分配方案：可选 `heap_*n*.c` 之一，也可使用你自行实现的方案。更多说明见第 3 章“堆内存管理”。


## 2.5 数据类型与编码风格指南

### 2.5.1 数据类型

每个 FreeRTOS port 都有一个独立的 `portmacro.h` 头文件，其中（除其他内容外）定义了两种与 port 相关的数据类型：`TickType_t` 和 `BaseType_t`。下面说明其宏/typedef 与实际类型：

- `TickType_t`

  FreeRTOS 会配置一个周期性中断，称为 tick 中断。

  FreeRTOS 应用启动以来发生的 tick 中断次数称为 *tick count*（节拍计数），它用于度量时间。

  两次 tick 中断之间的时间称为 *tick period*（节拍周期）。时间值通常以若干 tick 周期为单位表示。

  `TickType_t` 就是用于保存 tick count、以及表示时间参数的数据类型。

  `TickType_t` 可是无符号 16 位、32 位或 64 位类型，具体取决于 `FreeRTOSConfig.h` 中 `configTICK_TYPE_WIDTH_IN_BITS` 的配置。该配置与架构相关，FreeRTOS port 也会检查其有效性。

  在 8 位和 16 位架构上使用 16 位类型可显著提升效率，但会严重限制 FreeRTOS API 中可指定的最大阻塞时间。在 32 位或 64 位架构上，通常没有理由使用 16 位 `TickType_t`。

  旧配置 `configUSE_16_BIT_TICKS` 已被 `configTICK_TYPE_WIDTH_IN_BITS` 替代，以支持超过 32 位的 tick 计数。新设计应使用 `configTICK_TYPE_WIDTH_IN_BITS`，而非 `configUSE_16_BIT_TICKS`。

   <a name="tbl2" title="Table 2 TickType_t data type and the configTICK_TYPE_WIDTH_IN_BITS configuration"></a>

   * * *
   | configTICK\_TYPE\_WIDTH\_IN\_BITS | 8 位架构 | 16 位架构 | 32 位架构 | 64 位架构 |
   | --- | --- | --- | --- | --- |
   | TICK\_TYPE\_WIDTH\_16_BITS | uint16\_t | uint16\_t | uint16\_t | N/A |
   | TICK\_TYPE\_WIDTH\_32_BITS | uint32\_t | uint32\_t | uint32\_t | N/A |
   | TICK\_TYPE\_WIDTH\_64_BITS | N/A | N/A | uint64\_t | uint64\_t |

   ***表 2*** *`TickType_t` 数据类型与 `configTICK_TYPE_WIDTH_IN_BITS` 配置关系*
   * * *

- `BaseType_t`

  它总是被定义为目标架构上最高效的数据类型。通常：64 位架构上是 64 位类型，32 位架构上是 32 位类型，16 位架构上是 16 位类型，8 位架构上是 8 位类型。

  `BaseType_t` 通常用于返回值范围很小的函数，以及 `pdTRUE` / `pdFALSE` 这类布尔返回值。


*FreeRTOS 使用的 port 专属数据类型列表*


### 2.5.2 变量命名

变量名前缀体现其类型：`char` 用 `c`，`int16_t`（short）用 `s`，`int32_t`（long）用 `l`，`BaseType_t` 及其他非标准类型（结构体、任务句柄、队列句柄等）用 `x`。

若变量为无符号类型，再加前缀 `u`；若变量为指针，再加前缀 `p`。例如：`uint8_t` 变量前缀为 `uc`；`char *` 变量前缀为 `pc`。


### 2.5.3 函数命名

函数名前缀同时体现“返回类型”和“定义所在文件”。例如：

- `v`**Task**`PrioritySet()` 返回 *v*oid，定义在 **tasks**.c。
- `x`**Queue**`Receive()` 返回 *BaseType_t*，定义在 **queue**.c。
- `pv`**Timer**`GetTimerID()` 返回 *p*ointer to *v*oid，定义在 **timers**.c。

文件作用域（私有）函数以前缀 `prv` 命名。


### 2.5.4 格式风格

部分 demo 工程使用了 Tab，且约定一个 Tab 等于四个空格。内核代码现已不再使用 Tab。


### 2.5.5 宏命名

多数宏使用全大写，并以小写前缀表示宏的定义位置。表 3 给出了这些前缀。

<a name="tbl3" title="Table 3 Macro prefixes"></a>

* * *
| 前缀 | 宏定义位置 |
|----------------------------------------------|--------------------------------|
| port（例如 `portMAX_DELAY`）          | `portable.h` 或 `portmacro.h`  |
| task（例如 `taskENTER_CRITICAL()`）   | `task.h`                       |
| pd（例如 `pdTRUE`）                   | `projdefs.h`                   |
| config（例如 `configUSE_PREEMPTION`） | `FreeRTOSConfig.h`             |
| err（例如 `errQUEUE_FULL`）           | `projdefs.h`                   |

***表 3*** *宏前缀*
* * *

需要注意，信号量 API 几乎全部由宏实现，但其命名遵循“函数命名约定”，而不是“宏命名约定”。

表 4 的宏在 FreeRTOS 源码中被广泛使用。

<a name="tbl4" title="Table 4 Common macro definitions"></a>

* * *
| 宏 | 值 |
|--------------|-------|
| `pdTRUE`     | 1     |
| `pdFALSE`    | 0     |
| `pdPASS`     | 1     |
| `pdFAIL`     | 0     |

***表 4*** *常用宏定义*
* * *


### 2.5.6 大量类型转换的原因

FreeRTOS 源码需要在许多不同编译器上编译，而不同编译器生成警告的时机和规则并不一致，尤其是对类型转换（casting）的要求差异很大。因此，FreeRTOS 源码中包含了比通常更多的类型转换语句。
