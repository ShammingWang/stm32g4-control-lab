# STM32 MCU Learning

基于 NUCLEO-G431RB（STM32G431RBT6）的 MCU 与电机控制嵌入式学习项目。

支持的开发环境：

- macOS、Linux、Windows 或 WSL2
- VS Code
- STM32CubeIDE for Visual Studio Code（STMicroelectronics）
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

## 在新电脑上开始

1. 安装 VS Code 和 ST 官方的 STM32CubeIDE for Visual Studio Code 扩展。
2. 安装当前平台对应的 STM32CubeMX。
3. 克隆仓库：

   ```bash
   git clone https://github.com/ShammingWang/stm32g4-control-lab.git
   cd stm32g4-control-lab/blink_led
   ```

4. 在 VS Code 中打开 `blink_led`，执行 **Set Up STM32Cube project(s)**。扩展会为当前平台准备 CMake、Ninja、GNU Arm 工具链、Programmer 和 GDB Server。
5. 执行 **Build**，连接 NUCLEO-G431RB 后选择 ST-Link 调试运行。

工具安装路径、USB 设备名和调试器路径均属于本机配置，不纳入版本控制。构建产物位于工程的 `build/` 目录，也不纳入版本控制。

### macOS 提示

- Apple Silicon 和 Intel Mac 使用各自平台对应的扩展 Bundle；工程源码及 CMake 配置无需修改。
- 首次连接开发板时，若 macOS 请求 USB/附件访问权限，请允许 VS Code 或 ST 调试工具访问。
- 不需要 WSL、`usbipd` 或 Linux udev 规则。
- 若扩展未自动找到 CubeMX，在 VS Code 设置中为 STM32CubeMX executable path 选择本机安装路径，不要把该路径提交到仓库。

### WSL2 提示

WSL2 需要额外使用 `usbipd-win` 把 ST-Link 从 Windows 透传到 WSL，并安装 Linux 的 `libusb` 与 udev 规则。这只是 WSL 主机配置，不是工程依赖。
