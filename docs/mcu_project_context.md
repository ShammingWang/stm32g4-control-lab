# MCU / 电机控制嵌入式学习项目

> 本文档是本项目的长期上下文与 Agent 交接入口。  
> 新 Agent 开始教学或修改工程前，应先完整阅读本文，再检查当前工程与硬件状态。  
> 最后更新：2026-09-06

## 1. 核心目标

用户希望从现有软件工程背景逐步转向：

> **MCU 嵌入式软件工程师（电机控制器 / 电驱方向）**

最终希望进入摩托车、电摩、电驱、电控相关行业，尤其关注 **张雪机车**。同时关注：

- 九号、小牛电动
- 联合汽车电子（UAES）
- 汇川技术
- 海康机器人及相关嵌入式岗位
- 汽车电子、电驱、BMS、VCU、ECU、工业控制企业

可关注的岗位名称：

- MCU / 嵌入式软件工程师
- 电机控制器 / 电驱控制器软件工程师
- BMS、VCU、ECU 软件工程师
- 车载基础软件、BSW、Bootloader 工程师

路线重点是 **C + MCU + 实时控制 + 通信 + 电机控制**，不是以 Arduino、纯应用层或单纯标定为终点。

## 2. 用户背景与学习偏好

用户有计算机工程及软件开发背景，接触过：

- C、C++、Java、Python
- Linux
- 常规软件工程与调试流程

用户喜欢 C，特别关注：

- 指针、内存和位运算
- 中断、DMA、Timer
- 驱动、寄存器、Memory-Mapped I/O
- 实时系统和并发
- 硬件控制、性能与系统层问题

教学时不要从通用编程语法大篇幅讲起，但也不要假设用户理解 MCU、电子电路或 STM32 术语。遇到 BSP、HAL、CMSIS、GPIO、NVIC 等概念，应先用直白语言说明“它是什么、解决什么问题、最终落到哪一个硬件动作”。

用户偏好：

- 对话式、循序渐进的讲解
- 通过真实硬件现象建立直觉
- 不满足于背 API，希望持续追问“为什么”
- 少量代码配合调用链、寄存器和硬件解释
- 将知识映射到摩托车、电摩、电控实际场景
- 每次只引入少量新概念，避免一次倾倒大量理论

## 3. 当前硬件

### 主平台

- 开发板：**NUCLEO-G431RB**
- MCU：**STM32G431RBT6**
- 内核：Arm Cortex-M4
- 板载调试器：STLINK-V3
- 关键资源：高性能 ADC、Timer/PWM、DMA、FDCAN

选择 STM32G4 的原因是它适合实时控制、数字电源和电机控制，可自然延伸到 PMSM、BLDC 和 FOC。

### 已有器材

- NUCLEO-G431RB ×1
- ESP32 ×1
- 830 孔免焊面包板 ×1
- 公对公、公对母、母对母杜邦线
- 10kΩ 电位器 ×3
- 基础电子元件包（LED、电阻、电容等）
- SN65HVD230 经典 CAN 收发器模块 ×2
- Micro-USB 数据线

暂时不需要购买独立 ST-Link、示波器、焊台、电机或电机驱动板。逻辑分析仪、USB-CAN、CAN FD 收发器和电机套件按学习进度再决定。

### ESP32 的定位

STM32G431 是主学习平台。ESP32 后续可作为第二节点，模拟：

- BMS
- 仪表
- T-Box
- 另一个 CAN 节点

预期组合：

```text
ESP32（模拟 BMS / 仪表）
        │
        │ CAN
        ▼
STM32G431（模拟 VCU / 电机控制器）
```

注意：SN65HVD230 是物理层收发器。STM32 内部的 FDCAN 控制器不能直接连接 CAN_H/CAN_L。经典 CAN 阶段可使用现有模块；学习 CAN FD 时需要兼容 CAN FD 的收发器。

## 4. 跨平台开发约定与已验证环境

### 工程的跨平台约定

- 仓库必须能在 macOS、Linux、Windows 和 WSL2 之间迁移。
- 工程内只保存源码、`.ioc`、相对路径 CMake 配置及可复现的 Bundle 版本声明。
- 不得在受版本控制的文件中写死用户名、仓库绝对路径、工具安装绝对路径、USB BUSID 或串口设备名。
- `build/`、编辑器缓存和机器专属 `.vscode/` 配置不纳入版本控制。
- 换机器后，在具体工程目录执行 STM32 VS Code 扩展的 Setup，由扩展为当前操作系统安装对应工具。

GitHub 仓库：


```text
https://github.com/ShammingWang/stm32g4-control-lab
```

### 已验证的 WSL2 环境记录

以下内容仅记录第一台开发机的已验证状态，用于故障排查，不是项目运行前提：

- Windows + WSL2，Ubuntu 26.04
- VS Code Remote WSL
- STM32CubeMX 6.18.1（Linux x86_64）
- CMake + Ninja + GNU Arm Embedded GCC
- STM32CubeProgrammer + ST-Link GDB Server
- STLINK-V3 已成功编译、烧录和 GDB 调试

该机器的 CubeMX 曾安装在用户主目录，但绝对路径只保存在本机 VS Code 设置中，不应提交到仓库。

### STM32 工具的通用关系

- VS Code 扩展：STM32CubeIDE for Visual Studio Code（STMicroelectronics）
- 工具链：CMake + Ninja + GNU Arm Embedded GCC
- 下载/调试：STM32CubeProgrammer + ST-Link GDB Server

VS Code 插件使用平台专属 Bundle 管理工具；工具不一定出现在普通 shell 的 `PATH` 中，这不代表未安装。不要依赖某台机器的 Bundle 绝对目录。

### WSL2 专属 USB 说明

只在使用 WSL2 时，板载 ST-Link 需要通过 Windows `usbipd-win` 透传：

```powershell
usbipd bind --busid <BUSID>
usbipd attach --wsl --busid <BUSID>
```

USB 的 BUSID 在拔插或更换接口后可能变化，必须以 `usbipd list` 当前输出为准，不要永久假设是某个固定值。

首台 WSL 开发机已安装：

- `libusb-1.0-0`
- ST-Link udev 规则：`/etc/udev/rules.d/49-stlinkv*.rules`

该机器已经成功识别：

- STLINK-V3
- NUCLEO-G431RB
- 板载虚拟串口（具体设备名可能变化）

如果重启、拔插后 VS Code 报 `ST-Link not connected`，按以下顺序排查：

1. Windows 执行 `usbipd list`，确认设备是否为 `Attached`。
2. 必要时重新执行 `usbipd attach --wsl --busid <BUSID>`。
3. WSL 检查 `/dev/bus/usb` 权限。
4. 用 STM32CubeProgrammer 的 `--list` 做只读枚举。
5. 不要在 Windows 工具和 WSL 工具之间同时占用同一个 ST-Link。

macOS 原生开发不需要 `usbipd` 或 Linux udev 规则。克隆仓库后安装 macOS 版 VS Code 扩展与 CubeMX，在 `blink_led` 目录执行 Setup，再连接 ST-Link 构建和调试。

## 5. 当前工程与已完成进度

当前工程（相对仓库根目录）：

```text
blink_led/
```

关键内容：

- CubeMX 配置：`blink_led.ioc`
- 构建系统：CMake
- 编译器：GCC
- 目标芯片：STM32G431RBT6
- 系统时钟：170 MHz
- 板载绿色 LED：PA5
- 调试接口：SWD（PA13/PA14）
- 固件包：STM32CubeG4 v1.6.3

已经完成：

- CubeMX 以 NUCLEO-G431RB Board Selector 创建工程
- 生成 CMake 工程
- VS Code 插件完成 Setup
- GCC 编译和链接成功
- ST-Link 识别、烧录、GDB 调试成功
- 理解调试器默认停在 `main()` 属于正常的 stop-at-entry
- 使用 `BSP_LED_Toggle(LED_GREEN)` 点灯
- 使用 `HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5)` 点灯
- 当前进一步改为 `HAL_GPIO_WritePin()`，实现短亮/长灭实验
- 已沿 HAL 调用看到 `ODR` 和 `BSRR` 寄存器操作

当前循环大致为：

```c
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
HAL_Delay(100);
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
HAL_Delay(900);
```

用户已经理解的初步概念：

- `GPIOA` 表示 GPIO A 端口，`GPIO_PIN_5` 表示该端口第 5 个引脚，即 PA5
- BSP 面向具体开发板，HAL 面向 STM32 外设，寄存器直接对应硬件
- `ODR` 保存 GPIO 输出状态
- `BSRR` 低 16 位用于置位，高 16 位用于复位（此处复位指输出低电平）
- `BSRR` 是写命令型寄存器，可用一次写操作避免 ODR 的读—改—写丢失更新
- BSRR 的原子写有利于中断/RTOS 并发，但不能解决多个执行上下文争用同一引脚的业务逻辑竞争

尚未系统掌握：

- GPIO 初始化为什么必须先开 RCC 时钟
- GPIO `MODER/OTYPER/OSPEEDR/PUPDR` 的含义
- BSP/HAL/CMSIS 的完整边界
- 启动文件、链接脚本、向量表
- 中断、Timer、UART、ADC、PWM、DMA、RTOS、CAN

## 6. 教学方法与 Agent 行为约束

### 教学闭环

每一个主题尽量遵循：

```text
提出一个可观察目标
→ 用户亲手写少量代码
→ Build
→ 烧录/调试
→ 观察 LED、串口或测量值
→ 沿调用链下钻
→ 对照寄存器和硬件行为
→ 做一个小改造验证理解
→ 记录阶段结论
```

每课应包含：

- 本课目标
- 接线或板载资源说明
- 最少必要配置
- 少量需要用户亲手写的代码
- 预期现象
- 调试与验证方式
- HAL、寄存器和硬件之间的关系
- 常见错误
- 一个小练习或面试式追问

### 讲解约束

- 先解释用户当前看到的东西，不要突然跳到更高级主题。
- 不要用更多未解释的缩写去解释一个新缩写。
- 一次引入 1～3 个核心概念。
- 先建立直觉，再补严谨定义。
- 明确区分“软件约定”和“芯片硬件规定”。
- 明确区分初始化、运行时操作和调试工具链。
- 不要求背自动生成代码；教用户识别代码所有权、调用入口和故障定位路径。
- 用户提问基础概念时，不评价问题简单，应直接拆解。
- 不要一开始进入 AUTOSAR、复杂 FOC 数学或大型架构。

### 修改工程约束

- 修改前先检查当前文件和构建状态。
- CubeMX 生成文件中的用户代码优先放在：

```c
/* USER CODE BEGIN ... */
/* USER CODE END ... */
```

- 解释哪些文件归 CubeMX 管理，避免重新生成后覆盖用户代码。
- 每次修改后至少执行一次编译验证。
- 烧录前确认当前目标和 ST-Link 连接；不要盲目修改 USB/调试配置。
- 当前以 HAL 建立基础，再逐渐比较 LL 和寄存器写法；不要一开始完全抛弃 HAL。
- 教学工程保持简单，不要过早抽象出复杂框架。

## 7. 推荐工程组织

工作区保存整个学习路线；每个阶段使用一个独立、自包含的 STM32 工程，而不是每个微小实验都创建新工程。

建议逐步整理为：

```text
stm32/
├── docs/
│   └── mcu_project_context.md
├── lessons/
│   ├── 01_gpio_basics/
│   ├── 02_interrupt_timer/
│   ├── 03_uart_dma/
│   ├── 04_adc_pwm/
│   ├── 05_freertos/
│   └── 06_can_bus/
├── experiments/
└── capstone/
    └── motor_controller_sim/
```

当前暂不移动 `blink_led`，先用它完成 GPIO 基础。阶段结束后再整理为 `lessons/01_gpio_basics`。

使用 VS Code 时，优先单独打开当前 STM32 工程目录，避免多个 CMake 项目造成目标混淆。

## 8. 分阶段学习路线

### 阶段 1：GPIO 与 MCU 基本模型（当前阶段）

目标：建立“C 代码—寄存器—引脚电平—真实电路”的完整心智模型。

- GPIO 输出：Toggle、Set、Reset
- GPIO 初始化与 RCC 外设时钟
- `MODER/ODR/IDR/BSRR`
- 推挽、开漏、上拉、下拉
- 板载按钮输入
- 基础消抖
- Memory-Mapped I/O、`volatile`、位掩码
- BSP、HAL、LL、CMSIS 的层次

### 阶段 2：中断与定时器

- EXTI 外部中断
- NVIC 和中断优先级
- ISR 中应该做什么、不能做什么
- SysTick 与 `HAL_Delay`
- 通用定时器、PSC、ARR
- 用 Timer 取代阻塞延时

### 阶段 3：UART 与 DMA

- UART 波特率、帧格式
- 阻塞、轮询、中断、DMA 的差异
- ST-Link 虚拟串口
- 环形缓冲区
- DMA 与 CPU 协作

### 阶段 4：ADC 与 PWM

- 10kΩ 电位器作为 0～3.3V 分压输入
- ADC 原始值、参考电压、采样时间
- 滤波与异常检测
- Timer PWM、频率、占空比
- ADC 映射到 PWM，模拟电摩油门输入

```text
电位器 → ADC → 油门百分比 → 限幅/滤波 → PWM → LED 亮度
```

### 阶段 5：FreeRTOS

- Task、Scheduler、Priority
- Queue、Semaphore、Mutex、Event
- ISR 与 Task 配合
- 周期任务、实时性、资源所有权
- Watchdog 与故障恢复

### 阶段 6：CAN / FDCAN

- CAN 帧、ID、仲裁、位时序
- FDCAN Tx/Rx
- 收发器与 120Ω 终端电阻
- 双节点通信
- DBC 基础、超时、Alive Counter、Checksum
- 后续再学习 UDS 与 Bootloader

### 阶段 7：电机控制基础

在 MCU、Timer、ADC、PWM、DMA、RTOS、CAN 扎实之后进入：

```text
电机与逆变器基础
→ BLDC / PMSM
→ 闭环控制与 PID
→ Clarke / Park
→ Id / Iq
→ SVPWM
→ FOC
```

## 9. 长期作品项目

目标作品：

> **电动摩托车 VCU / 电机控制器模拟系统**

概念结构：

```text
油门电位器 ─ADC─┐
刹车输入 ───────┤
故障输入 ───────┤
                 ▼
             STM32G431
                 │
ESP32/BMS ─CAN───┤──→ 扭矩请求 / 限扭 / 安全状态
                 │
                 └──PWM──→ LED / 负载 / 后续电机驱动
```

最终项目应能在简历和面试中深入说明：

- 分层和模块边界为什么这样设计
- 控制周期和任务优先级如何选择
- ISR 与任务如何交换数据
- ADC 如何滤波并检测油门异常
- CAN 超时、BMS 禁止放电时如何进入安全状态
- PWM 如何生成
- Watchdog、故障码和降级策略如何设计
- 如何测试：单元测试、板上验证、故障注入、台架/HIL 思路

建议成熟阶段的软件结构：

```text
app/       torque、power_limit、state_machine、diagnosis
service/   can_service、bms、signal_timeout
driver/    adc、pwm、gpio、can、flash、watchdog
os/        tasks、queues、timing
```

不要在早期 GPIO 课程中提前套用这一整套架构。

## 10. 紧接着要做的事

当前下一课应继续使用 `blink_led` 工程，顺序建议为：

1. 验证 `HAL_GPIO_WritePin()` 的 SET/RESET 与 LED 电平关系。
2. 用调试器观察 GPIOA 的 `MODER` 和 `ODR` bit 5。
3. 进入 `BSP_LED_Init()` / `HAL_GPIO_Init()`，理解 PA5 为什么必须先被配置为输出。
4. 解释并验证 GPIOA 的 RCC 时钟使能。
5. 完成一个不同节奏的 LED 小练习。
6. 再进入板载按钮的轮询输入。

新 Agent 不应跳过当前 GPIO 初始化主题，直接带用户进入 RTOS、CAN 或 FOC。

## 11. 每次会话结束时的维护规则

完成一个有意义的里程碑后，更新本文档中的：

- `最后更新` 日期
- `当前开发环境`（工具、版本或路径变化时）
- `当前工程与已完成进度`
- `尚未系统掌握`
- `紧接着要做的事`

只记录已经验证的事实。不要把计划写成已完成，也不要记录密码、Token、邮箱、私有账号信息或不必要的设备序列号。
