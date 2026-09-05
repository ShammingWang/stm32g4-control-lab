# STM32 MCU Learning

基于 NUCLEO-G431RB（STM32G431RBT6）的 MCU 与电机控制嵌入式学习项目。

当前开发环境：

- VS Code Remote WSL
- STM32CubeMX
- CMake + Ninja + GNU Arm Embedded GCC
- STLINK-V3 + STM32CubeProgrammer

## 当前进度


- 完成编译、烧录与 GDB 调试链路
- GPIO 输出：BSP、HAL、ODR、BSRR
- GPIO 输入：板载按钮 PC13
- 当前实验：EXTI 外部中断控制板载 LED PA5

详细学习背景、路线、当前状态和 Agent 交接说明见 [mcu_project_context.md](mcu_project_context.md)。

## 工程

- `blink_led/`：GPIO 输出、输入与外部中断基础实验

## 构建

使用 STM32CubeIDE for Visual Studio Code 扩展打开具体工程目录并执行 Setup/Build。构建产物位于工程的 `build/` 目录，不纳入版本控制。
