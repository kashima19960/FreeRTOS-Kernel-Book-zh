# 2 FreeRTOS 内核分发包

## 2.1 简介

为帮助用户熟悉 FreeRTOS 内核的文件与目录结构，本章：

- 提供 FreeRTOS 目录结构的顶层视图。
- 描述任一 FreeRTOS 工程所需的源文件。
- 介绍示例应用。
- 提供如何创建新的 FreeRTOS 工程的信息。

此处描述仅针对官方 FreeRTOS 分发包。本书配套示例使用了略有不同的组织方式。


## 2.2 理解 FreeRTOS 分发包

### 2.2.1 定义：FreeRTOS Port（移植）

FreeRTOS 可使用大约二十种不同的编译器构建，并能运行在四十多种不同的处理器
架构上。每一种编译器与处理器的组合称为一个 FreeRTOS port。


### 2.2.2 构建 FreeRTOS

FreeRTOS 是一个库，为原本单线程、裸机的应用提供多任务能力。

FreeRTOS 以一组 C 源文件的形式提供。其中部分源文件对所有 port 通用，
另一些则为特定 port 所专有。将这些源文件作为工程的一部分进行构建，即可在
应用中使用 FreeRTOS API。每个官方 FreeRTOS port 都提供一个可作为参考的示例
应用。示例应用已预配置好需要构建的源文件并包含正确的头文件。

在其创建之时，每个示例应用都可“开箱即用”地构建，且无编译错误或警告。
如果后续构建工具的变更导致该情况不再成立，请通过 FreeRTOS 支持论坛
(<https://forums.FreeRTOS.org>) 告知我们。第 2.3 节介绍了示例应用。


### 2.2.3 FreeRTOSConfig.h

名为 FreeRTOSConfig.h 的头文件中定义的常量用于配置内核。不要在源文件中
直接包含 FreeRTOSConfig.h！应包含 FreeRTOS.h，它会在合适的时机包含
FreeRTOSConfig.h。

FreeRTOSConfig.h 用于将 FreeRTOS 内核裁剪以适配特定应用。例如，
FreeRTOSConfig.h 包含 `configUSE_PREEMPTION` 等常量，用于定义 FreeRTOS
采用协作式还是抢占式调度[^1]。

[^1]: 第 4.13 节描述了调度算法。

FreeRTOSConfig.h 为特定应用而定制，因此应放置在应用的一部分目录中，
而不是放在包含 FreeRTOS 源代码的目录中。

主 FreeRTOS 分发包为每个 FreeRTOS port 都包含一个示例应用，且每个示例应用
都有自己的 FreeRTOSConfig.h 文件。建议从所用 port 的示例应用所提供的
FreeRTOSConfig.h 开始并进行修改，而不是从头创建该文件。

FreeRTOS 参考手册以及 <https://www.freertos.org/a00110.html> 描述了
FreeRTOSConfig.h 中出现的常量。并非必须包含所有常量——很多在缺省时会使用
默认值。


### 2.2.4 官方分发包

各个 FreeRTOS 库（包括内核）可以从各自的 GitHub 仓库以及 zip 文件中获取。
在产品代码中使用 FreeRTOS 时，按库获取会更方便。然而入门时更建议下载主
FreeRTOS 分发包，因为它同时包含库与示例工程。

主分发包包含所有 FreeRTOS 库的源代码、所有 FreeRTOS 内核 port 以及所有
FreeRTOS 示例应用的工程文件。不要被文件数量吓到！应用只需要其中的一小部分。

可通过 <https://github.com/FreeRTOS/FreeRTOS/releases/latest> 下载包含
最新分发包的 zip 文件。或者使用以下 Git 命令从 GitHub 克隆主分发包，
并包含从各自 Git 仓库以子模块方式引入的独立库：

* * *
```
git clone https://github.com/FreeRTOS/FreeRTOS.git --recurse-submodules

git clone git@github.com:FreeRTOS/FreeRTOS.git --recurse-submodules
```
* * *


图 2.1 显示了 FreeRTOS 分发包的一级与二级目录：

<a name="fig2.1" title="图 2.1 FreeRTOS 分发包中的顶层目录"></a>

* * *
```
FreeRTOS
│ │
│ ├─Source  包含 FreeRTOS 内核源文件
│ │
│ └─Demo    包含预配置且与 port 相关的 FreeRTOS 内核示例工程
│
FreeRTOS-Plus
│
├─Source    包含其他 FreeRTOS 与生态系统库的源代码
│
└─Demo      包含其他 FreeRTOS 与生态系统库的示例工程
```
***图 2.1*** *FreeRTOS 分发包中的顶层目录*
* * *

分发包只包含一份 FreeRTOS 内核源文件；所有示例工程都假定内核源文件位于
FreeRTOS/Source 目录中，若更改目录结构，示例工程可能无法构建。


### 2.2.5 所有 Port 通用的 FreeRTOS 源文件

tasks.c 和 list.c 实现了 FreeRTOS 内核的核心功能，且总是必需的。它们位于
FreeRTOS/Source 目录，如图 2.2 所示。该目录还包含以下可选源文件：

-  **queue.c**

	queue.c 提供队列与信号量服务，详见本书后续内容。queue.c 几乎总是必需的。

-  **timers.c**

	timers.c 提供软件定时器功能，详见本书后续内容。仅当应用使用软件定时器时
	才需要构建。

-  **event\_groups.c**

	event\_groups.c 提供事件组功能，详见本书后续内容。仅当应用使用事件组时
	才需要构建。

-  **stream\_buffer.c**

	stream\_buffer.c 提供流缓冲区与消息缓冲区功能，详见本书后续内容。仅当
	应用使用流缓冲区或消息缓冲区时才需要构建。

-  **croutine.c**

	croutine.c 实现 FreeRTOS 协程功能。仅当应用使用协程时才需要构建。
	协程面向资源极其有限的微控制器，如今已很少使用。因此它们不再维护，
	新设计不建议使用。本书不再描述协程。


<a name="fig2.2" title="图 2.2 FreeRTOS 目录树中的核心源文件"></a>

* * *
```
FreeRTOS
│
└─Source
	 │
	 ├─tasks.c         FreeRTOS 源文件 - 必需
	 ├─list.c          FreeRTOS 源文件 - 必需
	 ├─queue.c         FreeRTOS 源文件 - 几乎总是必需
	 ├─timers.c        FreeRTOS 源文件 - 可选
	 ├─event_groups.c  FreeRTOS 源文件 - 可选
	 ├─stream_buffer.c FreeRTOS 源文件 - 可选
	 └─croutine.c      FreeRTOS 源文件 - 可选且不再维护
```
***图 2.2*** *FreeRTOS 目录树中的核心源文件*
* * *

我们认识到，zip 文件分发中使用的文件名可能引发命名空间冲突，因为很多工程
已经使用相同名称的文件。如有必要，用户可以更改文件名，但分发包中的文件名
不能更改，因为这会破坏与现有用户工程以及 FreeRTOS 相关开发工具的兼容性。


### 2.2.6 与 Port 相关的 FreeRTOS 源文件

FreeRTOS/Source/portable 目录包含特定 FreeRTOS port 的源文件。portable 目录
按层级组织，先按编译器，再按处理器架构。图 2.3 显示了其层级结构。

要在架构为 “*architecture*” 的处理器上使用 “*compiler*” 编译器运行
FreeRTOS，除核心 FreeRTOS 源文件外，还必须构建
FreeRTOS/Source/portable/\[*compiler*\]/\[*architecture*\] 目录中的文件。

如第 3 章《堆内存管理》所述，FreeRTOS 将堆内存分配视为可移植层的一部分。
如果 `configSUPPORT_DYNAMIC_ALLOCATION` 设为 0，则不要在工程中包含堆内存
分配方案。

FreeRTOS 在 FreeRTOS/Source/portable/MemMang 目录中提供了示例堆分配方案。
如果 FreeRTOS 配置为使用动态内存分配，则需要将该目录中的一个堆实现源文件
加入工程，或提供你自己的实现。

> **! 不要在工程中包含多个示例堆分配实现。**


<a name="fig2.3" title="图 2.3 FreeRTOS 目录树中的 port 专用源文件"></a>

* * *
```
FreeRTOS
│
└─Source
	 │
	 └─portable 目录包含所有与 port 相关的源文件
		  │
		  ├─MemMang 目录包含替代的堆分配源文件
		  │
		  ├─[compiler 1] 目录包含编译器 1 的 port 文件
		  │   │
		  │   ├─[architecture 1] 包含编译器 1、架构 1 的 port 文件
		  │   ├─[architecture 2] 包含编译器 1、架构 2 的 port 文件
		  │   └─[architecture 3] 包含编译器 1、架构 3 的 port 文件
		  │
		  └─[compiler 2] 目录包含编译器 2 的 port 文件
				│
				├─[architecture 1] 包含编译器 2、架构 1 的 port 文件
				├─[architecture 2] 包含编译器 2、架构 2 的 port 文件
				└─[etc.]
```
***图 2.3*** *FreeRTOS 目录树中的 port 专用源文件*
* * *

### 2.2.7 头文件包含路径

FreeRTOS 要求在编译器的 include 路径中加入三个目录：

1. FreeRTOS 内核头文件所在路径：FreeRTOS/Source/include。

2. 所用 FreeRTOS port 的专用源文件路径：
	FreeRTOS/Source/portable/\[*compiler*\]/\[*architecture*\]。

3. 正确的 FreeRTOSConfig.h 头文件路径。


### 2.2.8 头文件

使用 FreeRTOS API 的源文件必须包含 FreeRTOS.h，然后再包含对应 API 函数原型
所在的头文件——task.h、queue.h、semphr.h、timers.h、event_groups.h、
stream_buffer.h、message_buffer.h 或 croutine.h。不要显式包含任何其他 FreeRTOS
头文件——FreeRTOS.h 会自动包含 FreeRTOSConfig.h。


## 2.3 示例应用

每个 FreeRTOS port 至少包含一个示例应用，在其创建之时可以“开箱即用”地构建，
且无编译错误或警告。如果后续构建工具的变更导致该情况不再成立，请通过
FreeRTOS 支持论坛 (<https://forums.FreeRTOS.org>) 告知我们。

> **跨平台支持**：FreeRTOS 在 Windows、Linux 和 MacOS 系统上开发和测试，
> 同时覆盖多种工具链，包括嵌入式与传统工具链。然而，由于版本差异或测试遗漏，
> 有时仍会出现构建错误。请通过 FreeRTOS 支持论坛
> (<https://forums.FreeRTOS.org>) 反馈这些错误。

示例应用有以下几个目的：

- 提供一个可工作的、已预配置的工程示例，包含正确的文件，并设置了正确的编译器选项。
- 便于以最少的设置或先验知识进行“开箱即用”的实验。
- 演示如何使用 FreeRTOS API。
- 作为创建真实应用的基础。
- 对内核实现进行压力测试。

每个示例工程位于 FreeRTOS/Demo 目录下的一个独立子目录中。子目录名称表明该
示例工程对应的 port。

FreeRTOS.org 网站为每个示例应用提供一个网页。该网页包含以下信息：

- 如何在 FreeRTOS 目录结构中定位示例工程文件。
- 工程配置所使用的硬件或仿真器。
- 如何设置硬件以运行示例。
- 如何构建示例。
- 示例的预期行为。

所有示例工程都会创建一部分“通用示例任务”，其实现位于
FreeRTOS/Demo/Common/Minimal 目录。这些通用示例任务用于演示 FreeRTOS API 并
测试 FreeRTOS 内核的移植，它们并不实现任何特定的有用功能。

许多示例工程也可配置为创建一个简单的“blinky”风格入门工程，通常会创建两个
RTOS 任务和一个队列。

每个示例工程都包含一个名为 main.c 的文件，其中包含 `main()` 函数，
在启动 FreeRTOS 内核之前创建示例应用任务。有关特定示例的信息请参见各自
main.c 文件中的注释。

<a name="fig2.4" title="图 2.4 示例目录层级"></a>

* * *
```
FreeRTOS
	 │
	 └─Demo          目录包含所有示例工程
		  │
		  ├─[Demo x]  包含构建示例 “x” 的工程文件
		  ├─[Demo y]  包含构建示例 “y” 的工程文件
		  ├─[Demo z]  包含构建示例 “z” 的工程文件
		  └─Common    包含所有示例应用都会构建的文件
```
***图 2.4*** *示例目录层级*
* * *


## 2.4 创建 FreeRTOS 工程

### 2.4.1 改造提供的示例工程

每个 FreeRTOS port 至少带有一个预配置的示例应用。建议通过改造这些现有工程
来创建新工程，以确保新工程包含正确的文件、安装正确的中断处理程序，并设置
正确的编译器选项。

要基于现有示例工程创建新应用：

1. 打开提供的示例工程并确认其可按预期构建和运行。

2. 移除实现示例任务的源文件，这些文件位于 Demo/Common 目录。

3. 删除 `main()` 中除 `prvSetupHardware()` 和 `vTaskStartScheduler()` 之外的
	所有函数调用，如清单 2.1 所示。

4. 验证工程仍可构建。

完成上述步骤后，你将得到一个包含正确 FreeRTOS 源文件、但尚未定义具体功能的工程。


<a name="list2.1" title="清单 2.1 新 main() 函数模板"></a>


```c
int main( void )
{
	 /* 执行必要的硬件初始化。 */
	 prvSetupHardware();

	 /* --- 在此创建应用任务 --- */

	 /* 启动已创建的任务。 */
	 vTaskStartScheduler();

	 /* 只有在堆内存不足以启动调度器时才会执行到这里。 */
	 for( ;; );
	 return 0;
}
```
***清单 2.1*** *新 main() 函数模板*



### 2.4.2 从零创建新工程

如前所述，建议从现有示例工程创建新工程。如果这不可取，则按以下流程创建新工程：

1. 使用选定的工具链创建一个新工程，但暂不包含任何 FreeRTOS 源文件。

1. 确保新工程能够构建、下载到目标硬件并运行。

1. 只有在确认已有可用工程之后，才将表 1 中列出的 FreeRTOS 源文件加入工程。

1. 将所用 port 的示例工程中的 `FreeRTOSConfig.h` 头文件复制到你的新工程目录。

1. 将以下目录加入工程的头文件搜索路径：

	- FreeRTOS/Source/include
	- FreeRTOS/Source/portable/\[*compiler*\]/\[*architecture*\]
	  （其中 \[*compiler*\] 与 \[*architecture*\] 应与所选 port 相匹配）
	- `FreeRTOSConfig.h` 头文件所在目录

1. 复制相关示例工程的编译器设置。

1. 安装可能需要的 FreeRTOS 中断处理程序。可参考所用 port 的说明网页以及其
	示例工程的说明网页。

<a name="tbl1" title="表 1 工程需包含的 FreeRTOS 源文件"></a>

* * *
| 文件                            | 位置                          |
|---------------------------------|------------------------------|
| tasks.c                         | FreeRTOS/Source              |
| queue.c                         | FreeRTOS/Source              |
| list.c                          | FreeRTOS/Source              |
| timers.c                        | FreeRTOS/Source              |
| event\_groups.c                 | FreeRTOS/Source              |
| stream\_buffer.c                | FreeRTOS/Source              |
| 所有 C 与汇编文件               | FreeRTOS/Source/portable/\[compiler\]/\[architecture\] |
| heap\_n.c                       | FreeRTOS/Source/portable/MemMang，其中 n 为 1、2、3、4 或 5 |

***表 1*** *工程需包含的 FreeRTOS 源文件*
* * *

**关于堆内存的说明：**
如果 `configSUPPORT_DYNAMIC_ALLOCATION` 为 0，则不要在工程中包含堆内存分配方案。
否则需要在工程中包含堆内存分配方案，即选择一个 heap\_*n*.c 文件，或使用你
自己提供的实现。更多信息请参见第 3 章《堆内存管理》。


## 2.5 数据类型与编码风格指南

### 2.5.1 数据类型

每个 FreeRTOS port 都有一个独立的 portmacro.h 头文件，其中（除其他内容外）
定义了两个与 port 相关的数据类型：`TickType_t` 和 `BaseType_t`。下面列出其
所用宏/typedef 以及实际类型：

- `TickType_t`

  FreeRTOS 配置一个周期性中断，称为 tick 中断。

  FreeRTOS 应用启动以来发生的 tick 中断次数称为 *tick 计数*。tick 计数用于
  计时。

  两次 tick 中断之间的时间称为 *tick 周期*。时间以 tick 周期的倍数来表示。

  `TickType_t` 用于保存 tick 计数值并用于指定时间。

  `TickType_t` 可为无符号 16 位、32 位或 64 位类型，取决于 FreeRTOSConfig.h 中
  `configTICK_TYPE_WIDTH_IN_BITS` 的设置。该设置与架构相关。FreeRTOS 的 port
  也会检查该设置是否有效。

  在 8 位与 16 位架构上使用 16 位类型可显著提升效率，但会严重限制
  FreeRTOS API 调用中可指定的最大阻塞时间。在 32 位或 64 位架构上没有理由
  使用 16 位 `TickType_t`。

  早先使用的 `configUSE_16_BIT_TICKS` 已被 `configTICK_TYPE_WIDTH_IN_BITS`
  取代，以支持大于 32 位的 tick 计数。新设计应使用
  `configTICK_TYPE_WIDTH_IN_BITS` 而不是 `configUSE_16_BIT_TICKS`。

	<a name="tbl2" title="表 2 TickType_t 数据类型与 configTICK_TYPE_WIDTH_IN_BITS 配置"></a>

	* * *
	| configTICK\_TYPE\_WIDTH\_IN\_BITS | 8 位架构 | 16 位架构 | 32 位架构 | 64 位架构 |
	| --- | --- | --- | --- | --- |
	| TICK\_TYPE\_WIDTH\_16_BITS | uint16\_t | uint16\_t | uint16\_t | N/A |
	| TICK\_TYPE\_WIDTH\_32_BITS | uint32\_t | uint32\_t | uint32\_t | N/A |
	| TICK\_TYPE\_WIDTH\_64_BITS | N/A | N/A | uint64\_t | uint64\_t |

	***表 2*** *TickType_t 数据类型与 configTICK_TYPE_WIDTH_IN_BITS 配置*
	* * *

- `BaseType_t`

  该类型始终被定义为该架构上最高效的数据类型。通常，64 位架构使用 64 位类型，
  32 位架构使用 32 位类型，16 位架构使用 16 位类型，8 位架构使用 8 位类型。

  `BaseType_t` 通常用于返回值范围很有限的函数返回类型，也用于
  `pdTRUE`/`pdFALSE` 这类布尔值。


*FreeRTOS 使用的 port 特定数据类型列表*


### 2.5.2 变量命名

变量以其类型作为前缀：`char` 用 `c`，`int16_t`（short）用 `s`，`int32_t`（long）
用 `l`，`BaseType_t` 及其它非标准类型（结构体、任务句柄、队列句柄等）用 `x`。

若变量为无符号，还会加前缀 `u`；若变量为指针，还会加前缀 `p`。例如，
`uint8_t` 类型变量前缀为 `uc`，指向 `char` 的指针（`char *`）前缀为 `pc`。


### 2.5.3 函数命名

函数前缀由返回类型以及所在文件共同构成。例如：

- v**Task**PrioritySet() 返回 *v*oid，定义在 **tasks**.c 中。
- x**Queue**Receive() 返回 *BaseType_t*，定义在 **queue**.c 中。
- pv**Timer**GetTimerID() 返回指向 *v*oid 的 *p*ointer，定义在 **timers**.c 中。

文件作用域（私有）函数以 `prv` 作为前缀。


### 2.5.4 格式

部分示例应用使用制表符，其中一个制表符总是等于四个空格。内核已不再使用制表符。


### 2.5.5 宏命名

大多数宏使用大写，并以小写前缀指示宏的定义位置。表 3 给出了前缀列表。

<a name="tbl3" title="表 3 宏前缀"></a>

* * *
| 前缀                                        | 宏定义位置                     |
|---------------------------------------------|--------------------------------|
| port（例如 `portMAX_DELAY`）                | `portable.h` 或 `portmacro.h`  |
| task（例如 `taskENTER_CRITICAL()`）         | `task.h`                       |
| pd（例如 `pdTRUE`）                         | `projdefs.h`                   |
| config（例如 `configUSE_PREEMPTION`）       | `FreeRTOSConfig.h`             |
| err（例如 `errQUEUE_FULL`）                 | `projdefs.h`                   |

***表 3*** *宏前缀*
* * *

请注意，信号量 API 几乎完全由一组宏构成，但其命名遵循函数命名约定，而非宏命名约定。

表 4 中定义的宏在 FreeRTOS 源代码中普遍使用。

<a name="tbl4" title="表 4 常用宏定义"></a>

* * *
| 宏          | 值 |
|-------------|----|
| `pdTRUE`    | 1  |
| `pdFALSE`   | 0  |
| `pdPASS`    | 1  |
| `pdFAIL`    | 0  |

***表 4*** *常用宏定义*
* * *


### 2.5.6 过多类型转换的原因

FreeRTOS 源代码需要用许多不同编译器进行编译，而这些编译器在生成警告的方式和时机上
存在差异。尤其是，不同编译器希望以不同方式使用类型转换。因此，FreeRTOS 源代码
包含的类型转换比通常需要的更多。

```
