---
title: SEGGER RTT在STM32中的移植
toc: true
date: 2026-05-24 20:30:00
tags: [教程, STM32, RTT, DAP-Link]
---

基于pyOCD + DAPLink在STM32单片机上使用SEGGER RTT进行信息的输出

![J-Link_RTT](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2FJ-Link_RTT_1776997576.svg)

<!-- more -->

# 官方Overview
With RTT it is possible to output information from the target microcontroller as well as sending input to the application at a very high speed without affecting the target's real time behavior. SEGGER RTT can be used with any J-Link model and any supported target processor which allows background memory access, which are Cortex-M and RX targets.

简单来说，使用RTT就可以实现仅一个仿真器（DAP-Link、ST-Link、J-Link）就能输出信息，而不需要额外的外设，比如串口，并且速度极快。

# 移植使用

RTT兼容不同的编译器，这里以STM32CubeMX生成的CMake工程为例

## RTT驱动源码移植

### 下载

在github下载SEGGER RTT源代码，仓库链接：[SEGGERMicro/RTT: SEGGER RTT Sources](https://github.com/SEGGERMicro/RTT)
![RTT库文件截图](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2Fimage-20260424110324006_1776999804.png)

### 添加源码到工程目录

需要移植的文件有：

- `RTT/SEGGER_RTT.c`
- `RTT/SEGGER_RTT.h`
- `RTT/SEGGER_RTT_printf.c`
- `RTT/SEGGER_RTT_ASM_ARMv7M.S`
- `Config/SEGGER_RTT_Conf.h`

将以上5个文件拷贝到工程目录中，工程文件夹分类依据个人习惯，比如在工程中新建一个`Library`文件夹专门用于存放第三方库文件，在`Library`下创建`segger_rtt`存放SEGGER RTT源代码
![RTT移植文件](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2Fimage-20260424112107821_1777000867.png)
移植后的目录结构如下：

```text
RTT-demo/
├─ CMakeLists.txt
├─ CMakePresets.json
├─ RTT-demo.ioc
├─ startup_stm32g431xx.s
├─ STM32G431XX_FLASH.ld
├─ cmake/
├─ Core/
├─ Drivers/
└─ Library/
   └─ segger_rtt/
      ├─ SEGGER_RTT_ASM_ARMv7M.S
      ├─ SEGGER_RTT_Conf.h
      ├─ SEGGER_RTT_printf.c
      ├─ SEGGER_RTT.c
      └─ SEGGER_RTT.h
```

### 加入构建系统

注意：不同的开发工具或编译工具，操作步骤均不相同，这里以CMake为例
在`Library/segger_rtt`下创建`CMakeLists.txt`并写入以下内容：

```cmake
# Library/segger_rtt/CMakeLists.txt

set(RTT_SRCS
    "${CMAKE_CURRENT_SOURCE_DIR}/SEGGER_RTT.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/SEGGER_RTT_printf.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/SEGGER_RTT_ASM_ARMv7M.S"
)

target_sources(${CMAKE_PROJECT_NAME} PRIVATE ${RTT_SRCS})

target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/"
)
```

![编写RTT模块CMake](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2Fimage-20260424114235841_1777002155.png)

在项目根目录的`CMakeLists.txt`中，将`Library/segger_rtt`作为子模块添加，即增加一句：

```cmake
add_subdirectory(Library/segger_rtt)
```

![添加RTT为CMake子模块](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2Fimage-20260424114119472_1777002079.png)

## 编译和使用

在代码中引用`SEGGER_RTT.h`头文件
```c
/* USER CODE BEGIN Includes */
#include "SEGGER_RTT.h"
/* USER CODE END Includes */
```

在`main`函数中初始化
```c
/* USER CODE BEGIN Init */
SEGGER_RTT_Init();
/* USER CODE END Init */
```

在主循环中尝试调用打印函数，第一个参数默认写`0`即可
```c
/* Infinite loop */
/* USER CODE BEGIN WHILE */
int count = 0;
while (1)
{
    SEGGER_RTT_printf(0, "Hello World! Count: %d\n", count++);
    HAL_Delay(1000);
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
}
/* USER CODE END 3 */
```

## 输出预览

> [!NOTE]
>
> 如果使用的仿真器是J-Link，直接使用SEGGER官方提供的[J-Link RTT Viewer](https://www.segger.com/products/debug-probes/j-link/tools/rtt-viewer/)即可

如果是DAP-Link或ST-Link，可以使用pyOCD进行预览

### 安装pyOCD

直接使用pip命令安装即可：
注意：pyOCD依赖python运行环境，电脑上需要安装python运行环境，版本默认装最新
```shell
pip install pyocd -i https://pypi.tuna.tsinghua.edu.cn/simple
```

安装芯片包，这里以STM32G431CBU6为例：
```shell
pyocd pack install stm32g431cb
```

安装完成后，可以通过以下命令查看已有的芯片包，供后续[预览](#预览)使用：

```shell
pyocd list --targets
```

通过以下命令可以查看已连接的仿真器：

```shell
 pyocd list
```

### 预览

使用以下命令进入RTT预览模式：

```shell
pyocd rtt --target stm32g431cbux
```

其中`stm32g431cbux`应为当前使用的芯片型号，`x`为通配符

![RTT预览](https://cdn.jsdelivr.net/gh/ImZjm/ResourceHub/images%2F26%2F04%2F24%2Fimage-20260424170410088_1777021450.png)
