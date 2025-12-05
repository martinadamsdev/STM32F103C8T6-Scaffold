# STM32F103C8T6 项目

基于 STM32F103C8T6 (Cortex-M3) 的嵌入式项目，使用 CMake 构建系统。

## 硬件规格

- **MCU**: STM32F103C8T6
- **内核**: ARM Cortex-M3
- **Flash**: 64KB
- **RAM**: 20KB
- **时钟**: 最高 72MHz
- **外设**: GPIO, UART, SPI, I2C, ADC, Timer 等

## 快速开始

### macOS M1/M2/M3 开发环境搭建

#### 1. 安装必需工具

```bash
# 安装 Homebrew（如果未安装）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装 ARM 工具链
brew install --cask gcc-arm-embedded

# 安装 CMake 和 Ninja
brew install cmake ninja

# 安装烧录工具（可选）
brew install stlink
```

#### 2. 安装 IDE 和扩展

**选项 A: Visual Studio Code**
```bash
# 安装 VS Code
brew install --cask visual-studio-code

# 安装必需的扩展（一键安装）
code --install-extension ms-vscode.cpptools && \
code --install-extension ms-vscode.cmake-tools && \
code --install-extension marus25.cortex-debug && \
code --install-extension stmicroelectronics.stm32-vscode-extension && \
code --install-extension llvm-vs-code-extensions.vscode-clangd && \
code --install-extension ms-vscode.hexeditor
```

**扩展说明：**
- `ms-vscode.cpptools` - C/C++ IntelliSense 和调试
- `ms-vscode.cmake-tools` - CMake 集成
- `marus25.cortex-debug` - ARM Cortex 调试支持
- `stmicroelectronics.stm32-vscode-extension` - STM32 官方扩展
- `llvm-vs-code-extensions.vscode-clangd` - Clangd 语言服务器
- `ms-vscode.hexeditor` - 十六进制编辑器（查看 .bin/.hex 文件）

**选项 B: CLion**
```bash
# 安装 CLion
brew install --cask clion

# CLion 需要配置 CMake Toolchain
# 参考 .clion-setup.md 文件
```

#### 3. 验证安装

```bash
# 检查 ARM 工具链
arm-none-eabi-gcc --version
# 应该显示: gcc version 14.x.x 或更高

# 检查 CMake
cmake --version
# 应该显示: cmake version 3.20 或更高

# 检查 Ninja
ninja --version
```

#### 4. 克隆项目并构建

```bash
# 克隆项目
git clone <repository-url>
cd test32

# 配置 Debug 构建
cmake --preset Debug

# 编译
cmake --build --preset Debug

# 输出文件位于 build/Debug/
ls -lh build/Debug/*.{elf,hex,bin}
```

#### 5. 烧录固件到 STM32

```bash
# 连接 ST-Link 调试器到 Mac 和 STM32

# 使用 st-flash 烧录
st-flash write build/Debug/test32.bin 0x8000000

# 或使用 STM32CubeProgrammer（GUI）
# 下载地址: https://www.st.com/en/development-tools/stm32cubeprog.html
```

---

### Windows 开发环境搭建

#### 1. 安装必需工具

**方式 A: 使用 STM32CubeIDE（推荐，包含所有工具）**

1. 下载并安装 [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
   - 包含 ARM 工具链、CMake、GDB、烧录工具等
   - 安装路径示例：`C:\ST\STM32CubeIDE_1.16.0\`

2. 将工具链添加到系统 PATH：
   ```
   C:\ST\STM32CubeIDE_1.16.0\STM32CubeIDE\plugins\com.st.stm32cube.ide.mcu.externaltools.gnu-tools-for-stm32.12.3.rel1.win32_1.0.200.202406191623\tools\bin
   ```

**方式 B: 手动安装各个工具**

1. 安装 [ARM GNU Toolchain](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)
   - 选择 arm-none-eabi 版本
   - 安装时勾选 "Add path to environment variable"

2. 安装 [CMake](https://cmake.org/download/)
   - 下载 Windows Installer
   - 安装时选择 "Add CMake to the system PATH"

3. 安装 [Ninja](https://github.com/ninja-build/ninja/releases)
   - 下载 ninja-win.zip
   - 解压到 `C:\Program Files\Ninja\`
   - 手动添加到 PATH

4. 安装 [Git for Windows](https://git-scm.com/download/win)

#### 2. 安装 IDE

**选项 A: Visual Studio Code**

1. 下载并安装 [VS Code](https://code.visualstudio.com/)

2. 在 PowerShell 中安装扩展（一键安装）：
   ```powershell
   code --install-extension ms-vscode.cpptools
   code --install-extension ms-vscode.cmake-tools
   code --install-extension marus25.cortex-debug
   code --install-extension stmicroelectronics.stm32-vscode-extension
   code --install-extension llvm-vs-code-extensions.vscode-clangd
   code --install-extension ms-vscode.hexeditor
   ```

**扩展说明：**
- `ms-vscode.cpptools` - C/C++ IntelliSense 和调试
- `ms-vscode.cmake-tools` - CMake 集成
- `marus25.cortex-debug` - ARM Cortex 调试支持
- `stmicroelectronics.stm32-vscode-extension` - STM32 官方扩展
- `llvm-vs-code-extensions.vscode-clangd` - Clangd 语言服务器
- `ms-vscode.hexeditor` - 十六进制编辑器（查看 .bin/.hex 文件）

**选项 B: CLion**

1. 下载并安装 [CLion](https://www.jetbrains.com/clion/)

2. 配置 CMake Toolchain（参考 `.clion-setup.md`）

#### 3. 验证安装

打开 PowerShell 或 CMD：

```powershell
# 检查 ARM 工具链
arm-none-eabi-gcc --version

# 检查 CMake
cmake --version

# 检查 Ninja
ninja --version

# 检查 Git
git --version
```

#### 4. 克隆项目并构建

```powershell
# 克隆项目
git clone <repository-url>
cd test32

# 配置 Debug 构建
cmake --preset Debug

# 编译
cmake --build --preset Debug

# 查看输出文件
dir build\Debug\*.elf
dir build\Debug\*.hex
dir build\Debug\*.bin
```

#### 5. 烧录固件到 STM32

**方式 A: 使用 STM32CubeProgrammer（推荐）**

1. 下载并安装 [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html)

2. 连接 ST-Link 到 PC 和 STM32

3. 打开 STM32CubeProgrammer GUI：
   - 选择连接方式：ST-LINK
   - 点击 "Connect"
   - 选择文件：`build\Debug\test32.hex` 或 `test32.bin`
   - 点击 "Download"

**方式 B: 使用命令行工具**

```powershell
# 使用 STM32CubeProgrammer CLI
# 假设安装在默认路径
& "C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe" `
  -c port=SWD -w build\Debug\test32.hex -v -rst

# 或使用 st-flash（需要单独安装 stlink 工具）
st-flash write build\Debug\test32.bin 0x8000000
```

---

### 常见构建命令

不论在 macOS 还是 Windows，以下命令都适用：

```bash
# 使用 CMake Presets（推荐）
cmake --preset Debug          # 配置 Debug 构建
cmake --build --preset Debug  # 编译 Debug

cmake --preset Release          # 配置 Release 构建
cmake --build --preset Release  # 编译 Release

# 清理构建
cmake --build --preset Debug --target clean

# 重新构建
cmake --build --preset Debug --clean-first
```

### 输出文件说明

构建成功后会生成以下文件：

- `test32.elf` - ELF 格式可执行文件（包含调试符号）
- `test32.hex` - Intel HEX 格式固件（常用于烧录）
- `test32.bin` - 纯二进制固件（可直接写入 Flash）
- `test32.map` - 内存映射文件（查看符号地址和内存使用）

## 项目结构

```
.
├── CMakeLists.txt              # 主构建配置（用户可修改）
├── CMakePresets.json           # CMake 预设配置（Debug/Release）
├── stm32f103x8_flash.ld       # 链接脚本（定义内存布局）
├── .gitignore                  # Git 忽略规则
├── .clangd                     # Clangd 语言服务器配置
│
├── Src/                        # 源代码目录
│   ├── main.c                 # 应用程序主入口
│   ├── syscall.c              # 系统调用实现（printf 等）
│   ├── sysmem.c               # 内存分配实现（malloc/free）
│   └── startup_stm32f103xx.S  # 启动代码和中断向量表
│
├── Inc/                        # 用户头文件目录（当前为空）
│
├── cmake/                      # CMake 配置文件目录
│   ├── gnu-tools-for-stm32.cmake    # ARM 工具链配置（定义编译器）
│   └── vscode_generated.cmake       # 自动生成（不要手动修改）
│
├── .vscode/                    # VS Code 配置
│   ├── settings.json          # 编辑器和工具设置
│   └── c_cpp_properties.json  # C/C++ IntelliSense 配置
│
├── .settings/                  # STM32CubeIDE 设置（保留配置文件）
│   ├── bundles-lock.store.json
│   ├── bundles.store.json
│   └── ide.store.json
│
├── build/                      # 构建输出目录（使用 preset 时）
│   ├── Debug/                 # Debug 构建产物
│   └── Release/               # Release 构建产物
│
├── cmake-build-debug/          # CLion Debug 构建目录
├── cmake-build-release/        # CLion Release 构建目录
│
├── README.md                   # 项目说明（本文件）
├── CLAUDE.md                   # 架构和构建系统详解
├── CONTRIBUTING.md             # 协同开发指南
└── .clion-setup.md            # CLion 配置说明
```

### 核心文件说明

#### 构建配置文件

- **CMakeLists.txt**
  - 用户可修改的主构建配置
  - 定义源文件列表、包含目录、编译选项
  - 包含 `cmake/vscode_generated.cmake` 获取自动生成的配置

- **CMakePresets.json**
  - 预定义的构建配置（Debug/Release）
  - 包含 CMSIS 设备参数（CPU 型号、字节序等）
  - 指定工具链文件和构建目录

- **stm32f103x8_flash.ld**
  - 链接脚本，定义内存布局
  - 指定 Flash (64KB @ 0x08000000) 和 RAM (20KB @ 0x20000000)
  - 定义堆栈大小和段位置

#### 源代码文件

- **Src/main.c**
  - 应用程序入口点
  - 包含 `main()` 函数
  - 当前是空的无限循环，等待你添加功能

- **Src/startup_stm32f103xx.S**
  - ARM 汇编启动代码
  - 定义中断向量表（Reset_Handler, 各种中断处理函数）
  - 初始化 .data 段（从 Flash 复制到 RAM）
  - 清零 .bss 段
  - 调用 main() 函数

- **Src/syscall.c**
  - 系统调用实现（_write, _read, _close 等）
  - 支持 printf 输出（需要配置 UART 才能实际输出）
  - 提供标准 C 库所需的底层接口

- **Src/sysmem.c**
  - 内存管理实现
  - 提供 _sbrk() 函数用于 malloc/free
  - 管理堆内存分配

#### CMake 配置文件

- **cmake/gnu-tools-for-stm32.cmake**
  - ARM 交叉编译工具链定义
  - 根据 CMSIS_Dcore 设置 CPU 标志（-mcpu=cortex-m3）
  - 配置 arm-none-eabi-gcc/g++ 编译器路径
  - 设置编译选项（-fdata-sections, -ffunction-sections 等）

- **cmake/vscode_generated.cmake**
  - **不要手动修改此文件**
  - 由 STM32CubeIDE 自动生成
  - 包含链接脚本路径、符号定义等

#### IDE 配置文件

- **.vscode/settings.json**
  - VS Code 工作区设置
  - 配置 CMake 使用 cube-cmake
  - 配置 clangd 使用 STM32 工具链

- **.clangd**
  - Clangd 语言服务器配置
  - 指定编译数据库位置（build/Debug）
  - 配置诊断信息和错误限制

#### 文档文件

- **README.md** - 项目快速入门和概览
- **CLAUDE.md** - 详细的架构说明和构建流程
- **CONTRIBUTING.md** - 协同开发规范和流程
- **.clion-setup.md** - CLion IDE 配置指南

## 开发

### 添加新功能

1. 在 `Src/` 创建 `.c` 文件
2. 在 `Inc/` 创建对应的 `.h` 文件
3. 更新 `CMakeLists.txt` 中的 `sources_SRCS`
4. 重新构建项目

### 构建配置

- **Debug**: 未优化，包含调试符号
  - 编译选项: `-O0 -g3 -ggdb`
  - 用于开发和调试
  - 文件较大但便于调试

- **Release**: 优化体积，无调试符号
  - 编译选项: `-Os`
  - 用于生产部署
  - 文件更小，性能更好

## 硬件调试

### 使用 ST-Link 调试器

#### macOS 调试配置

1. **安装 OpenOCD**
   ```bash
   brew install openocd
   ```

2. **启动 OpenOCD GDB Server**
   ```bash
   # 使用 ST-Link V2
   openocd -f interface/stlink.cfg -f target/stm32f1x.cfg

   # 或使用 ST-Link V2-1
   openocd -f interface/stlink-v2-1.cfg -f target/stm32f1x.cfg
   ```

3. **在另一个终端启动 GDB**
   ```bash
   arm-none-eabi-gdb build/Debug/test32.elf

   # 在 GDB 中连接到 OpenOCD
   (gdb) target remote localhost:3333
   (gdb) monitor reset halt
   (gdb) load
   (gdb) continue
   ```

4. **VS Code 调试配置**

   在 `.vscode/launch.json` 中添加：
   ```json
   {
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Debug STM32 (OpenOCD)",
         "type": "cppdbg",
         "request": "launch",
         "program": "${workspaceFolder}/build/Debug/test32.elf",
         "cwd": "${workspaceFolder}",
         "MIMode": "gdb",
         "miDebuggerPath": "/opt/homebrew/bin/arm-none-eabi-gdb",
         "miDebuggerServerAddress": "localhost:3333",
         "setupCommands": [
           {
             "text": "target remote localhost:3333"
           },
           {
             "text": "monitor reset halt"
           },
           {
             "text": "load"
           }
         ]
       }
     ]
   }
   ```

#### Windows 调试配置

1. **使用 STM32CubeIDE 内置的 GDB Server**

   STM32CubeIDE 已包含 ST-Link GDB Server，默认路径：
   ```
   C:\ST\STM32CubeIDE_1.16.0\STM32CubeIDE\plugins\com.st.stm32cube.ide.mcu.externaltools.stlink-gdb-server.win32_2.x.x\tools\bin\ST-LINK_gdbserver.exe
   ```

2. **启动 ST-Link GDB Server**
   ```powershell
   # 在 PowerShell 中运行
   & "C:\ST\STM32CubeIDE_1.16.0\STM32CubeIDE\plugins\com.st.stm32cube.ide.mcu.externaltools.stlink-gdb-server.win32_2.x.x\tools\bin\ST-LINK_gdbserver.exe" `
     -p 3333 -m 0 -d
   ```

3. **启动 GDB 调试**
   ```powershell
   arm-none-eabi-gdb build\Debug\test32.elf

   # 在 GDB 中连接
   (gdb) target remote localhost:3333
   (gdb) monitor reset halt
   (gdb) load
   (gdb) continue
   ```

4. **VS Code 调试配置**

   在 `.vscode/launch.json` 中添加：
   ```json
   {
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Debug STM32 (ST-Link)",
         "type": "cppdbg",
         "request": "launch",
         "program": "${workspaceFolder}/build/Debug/test32.elf",
         "cwd": "${workspaceFolder}",
         "MIMode": "gdb",
         "miDebuggerPath": "arm-none-eabi-gdb",
         "miDebuggerServerAddress": "localhost:3333",
         "setupCommands": [
           {
             "text": "target remote localhost:3333"
           },
           {
             "text": "monitor reset halt"
           },
           {
             "text": "load"
           }
         ]
       }
     ]
   }
   ```

### CLion 调试配置

参考 `.clion-setup.md` 文件中的"调试配置"章节。

### 常用 GDB 调试命令

```gdb
# 设置断点
break main
break Src/main.c:23

# 查看变量
print variable_name
print/x register_value  # 十六进制显示

# 单步执行
step     # 步进（进入函数）
next     # 步过（跳过函数）
continue # 继续运行

# 查看寄存器
info registers
info registers r0 r1 r2

# 查看内存
x/16xw 0x20000000  # 查看 RAM 起始的 16 个字（word）
x/64xb 0x08000000  # 查看 Flash 起始的 64 个字节

# 查看调用栈
backtrace
frame 0

# 重置并重新运行
monitor reset halt
load
continue
```

### 串口调试

如果需要使用 printf 进行串口输出调试：

1. 修改 `Src/syscall.c` 中的 `_write` 函数，将输出重定向到 UART
2. 配置 UART 硬件（波特率通常为 115200）
3. 使用串口工具连接：

   **macOS:**
   ```bash
   # 查找串口设备
   ls /dev/tty.*

   # 使用 screen 连接
   screen /dev/tty.usbserial-XXX 115200

   # 或使用 minicom
   brew install minicom
   minicom -D /dev/tty.usbserial-XXX -b 115200
   ```

   **Windows:**
   - 使用 PuTTY 或 TeraTerm
   - 配置 COM 端口和波特率 115200

## 文档

### 核心文档
- [CLAUDE.md](CLAUDE.md) - **项目架构和构建系统详解**（Claude Code 配置）
- [CONTRIBUTING.md](CONTRIBUTING.md) - 协同开发规范和 Git 提交规范
- [LICENSE](LICENSE) - 软件许可证

### 详细文档 (docs/)
- [文档索引](docs/README.md) - 📚 完整的文档导航
- [数据手册指南](docs/DATASHEETS.md) - 📖 中英文手册获取途径和学习路径
- [代码示例](docs/EXAMPLES.md) - GPIO、UART、定时器等示例代码
- [故障排除](docs/TROUBLESHOOTING.md) - 常见问题和解决方案
- [CLion 配置](docs/clion-setup.md) - CLion IDE 详细配置指南
- [项目总结](docs/PROJECT_SUMMARY.md) - 完整的项目功能和文档清单
- [文档组织说明](docs/DOCUMENTATION_STRUCTURE.md) - 文档结构最佳实践

## 内存限制

注意 STM32F103C8T6 的资源限制：

- Flash: 64KB（代码和常量数据）
- RAM: 20KB（全局变量、堆、栈）
- 最小堆: 512 字节
- 最小栈: 1024 字节

构建时会自动显示内存使用情况。

## 许可证

Copyright (c) 2025 STMicroelectronics.
All rights reserved.
