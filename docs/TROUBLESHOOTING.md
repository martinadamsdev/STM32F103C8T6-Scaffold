# 故障排除指南

本文档列出了 STM32 开发过程中常见的问题和解决方案。

## 构建问题

### 问题：找不到 arm-none-eabi-gcc

**症状：**
```
-- The C compiler identification is unknown
CMake Error: Could not find arm-none-eabi-gcc
```

**解决方案：**

**macOS:**
```bash
# 检查是否安装
which arm-none-eabi-gcc

# 如果未安装
brew install --cask gcc-arm-embedded

# 重启终端或刷新环境变量
source ~/.zshrc  # 或 source ~/.bash_profile
```

**Windows:**
```powershell
# 检查 PATH 环境变量
$env:PATH -split ';' | Select-String "arm-none-eabi"

# 如果没有找到，手动添加到系统 PATH
# 例如：C:\Program Files (x86)\GNU Arm Embedded Toolchain\13.3\bin
```

---

### 问题：CMake 找不到 Ninja

**症状：**
```
CMake Error: CMake was unable to find a build program corresponding to "Ninja"
```

**解决方案：**

**macOS:**
```bash
brew install ninja
```

**Windows:**
```powershell
# 下载 Ninja
# https://github.com/ninja-build/ninja/releases
# 解压并添加到 PATH
```

---

### 问题：set_target_properties 错误

**症状：**
```
CMake Error at CMakeLists.txt:92 (set_target_properties):
  set_target_properties called with incorrect number of arguments.
```

**解决方案：**

这个问题已在项目中修复。确保 `CMakeLists.txt` 包含以下内容：

```cmake
# Set default PROJECT_ORIGINAL_NAME if not provided by preset
if(NOT DEFINED PROJECT_ORIGINAL_NAME)
    set(PROJECT_ORIGINAL_NAME "${PROJECT_NAME}")
endif()
```

如果仍有问题，删除构建目录并重新配置：
```bash
rm -rf build cmake-build-*
cmake --preset Debug
```

---

### 问题：链接脚本错误

**症状：**
```
cannot open linker script file stm32f103x8_flash.ld: No such file or directory
```

**解决方案：**

检查链接脚本文件是否存在：
```bash
ls -l stm32f103x8_flash.ld
```

如果不存在，从 STM32CubeIDE 或 STM32Cube HAL 包中获取对应型号的链接脚本。

---

### 问题：编译警告 "unused variables"

**症状：**
```
CMake Warning:
  Manually-specified variables were not used by the project:
    CMSIS_Dfpu
    CMSIS_Ddsp
    ...
```

**解决方案：**

这是正常的警告，可以忽略。这些变量在 `CMakePresets.json` 中定义，但对于 Cortex-M3（无 FPU/DSP）来说不适用。

---

## 烧录问题

### 问题：st-flash 找不到设备

**症状：**
```
st-flash: error while loading shared libraries
# 或
Failed to connect to target
```

**解决方案：**

1. **检查 ST-Link 连接**
   - 确保 ST-Link 正确连接到 USB 和 STM32
   - 检查 SWDIO、SWCLK、GND、3.3V 连接

2. **检查设备权限（Linux/macOS）**
   ```bash
   # macOS
   ls -l /dev/cu.usbmodem*

   # Linux - 添加 udev 规则
   sudo nano /etc/udev/rules.d/49-stlinkv2.rules
   ```

   添加以下内容：
   ```
   SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE:="0666"
   SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", MODE:="0666"
   ```

   然后重新加载 udev 规则：
   ```bash
   sudo udevadm control --reload-rules
   sudo udevadm trigger
   ```

3. **更新 ST-Link 固件**
   - 下载 STSW-LINK007 固件更新工具
   - https://www.st.com/en/development-tools/stsw-link007.html

---

### 问题：STM32CubeProgrammer 无法连接

**症状：**
```
Error: Target not found
```

**解决方案：**

1. **检查连接模式**
   - 确保选择了正确的连接接口（ST-LINK）
   - 尝试降低连接速度（从 4000 kHz 降到 1000 kHz）

2. **检查 NRST 引脚**
   - 如果使用了 NRST 引脚，确保正确连接
   - 尝试勾选 "Connect Under Reset" 选项

3. **检查电源**
   - 确保 STM32 有稳定的 3.3V 供电
   - 检查去耦电容是否正确连接

4. **尝试批量擦除**
   ```bash
   st-flash erase
   ```

---

## 调试问题

### 问题：OpenOCD 无法启动

**症状：**
```
Error: libusb_open() failed with LIBUSB_ERROR_ACCESS
```

**解决方案：**

**macOS:**
```bash
# 使用 sudo 运行（不推荐长期使用）
sudo openocd -f interface/stlink.cfg -f target/stm32f1x.cfg

# 更好的解决方案：修复权限
# 安装 libusb
brew install libusb
```

**Linux:**
```bash
# 添加用户到 dialout 组
sudo usermod -a -G dialout $USER
sudo usermod -a -G plugdev $USER

# 注销并重新登录
```

---

### 问题：GDB 无法连接

**症状：**
```
(gdb) target remote localhost:3333
localhost:3333: Connection refused.
```

**解决方案：**

1. **确保 OpenOCD 正在运行**
   ```bash
   # 在另一个终端检查
   lsof -i :3333
   ```

2. **检查防火墙设置**
   - macOS: System Settings → Network → Firewall
   - Windows: Windows Defender Firewall

3. **使用正确的配置文件**
   ```bash
   # ST-Link V2
   openocd -f interface/stlink.cfg -f target/stm32f1x.cfg

   # ST-Link V2-1
   openocd -f interface/stlink-v2-1.cfg -f target/stm32f1x.cfg
   ```

---

### 问题：断点不起作用

**症状：**
程序不在断点处停止

**解决方案：**

1. **使用 Debug 构建**
   ```bash
   cmake --preset Debug
   cmake --build --preset Debug
   ```

2. **检查优化级别**
   - Debug 应该是 `-O0`
   - Release 使用 `-Os` 会内联函数，导致断点失效

3. **使用硬件断点**
   ```gdb
   (gdb) hbreak main
   ```

   注意：Cortex-M3 只有 6 个硬件断点

---

## 串口调试问题

### 问题：printf 没有输出

**原因：**
默认的 `syscall.c` 中 `_write` 函数未连接到 UART。

**解决方案：**

修改 `Src/syscall.c`，将 `_write` 重定向到 UART：

```c
int _write(int file, char *ptr, int len)
{
    // 替换为实际的 UART 发送函数
    for(int i = 0; i < len; i++) {
        // 等待 UART 发送完成
        while(!(USART1->SR & USART_SR_TXE));
        USART1->DR = ptr[i];
    }
    return len;
}
```

---

### 问题：串口乱码

**原因：**
波特率不匹配

**解决方案：**

1. **检查 UART 配置**
   - STM32 端：通常配置为 115200 波特率
   - 串口终端：确保设置相同波特率

2. **检查时钟配置**
   - 确保系统时钟正确配置
   - 检查 UART 时钟源

---

## 内存问题

### 问题：Stack overflow

**症状：**
程序运行一段时间后崩溃或进入 HardFault

**解决方案：**

增加栈大小，编辑 `stm32f103x8_flash.ld`：

```ld
_Min_Heap_Size = 0x200;  /* 保持不变 */
_Min_Stack_Size = 0x800; /* 从 0x400 增加到 0x800 */
```

---

### 问题：Heap exhausted

**症状：**
```
malloc() returns NULL
```

**解决方案：**

1. **减少动态内存使用**
   - 使用栈分配而不是堆分配
   - 使用静态数组而不是 malloc

2. **增加堆大小**（如果确实需要）

   编辑 `stm32f103x8_flash.ld`：
   ```ld
   _Min_Heap_Size = 0x400;  /* 从 0x200 增加 */
   ```

   注意：STM32F103C8T6 只有 20KB RAM，需要平衡堆栈大小

---

### 问题：Flash overflow

**症状：**
```
region `FLASH' overflowed by XXX bytes
```

**解决方案：**

1. **使用 Release 构建**
   ```bash
   cmake --preset Release
   cmake --build --preset Release
   ```

2. **检查代码大小**
   ```bash
   arm-none-eabi-size build/Debug/test32.elf
   ```

3. **优化措施**
   - 移除未使用的库和代码
   - 使用 `-Os` 优化编译选项（Release 已启用）
   - 考虑升级到更大 Flash 的芯片（如 STM32F103CB - 128KB）

---

## IDE 特定问题

### CLion: 代码补全不工作

**解决方案：**

1. **重新加载 CMake 项目**
   - Tools → CMake → Reload CMake Project

2. **检查 Toolchain 配置**
   - Settings → Build, Execution, Deployment → CMake
   - 确保 CMake options 包含 `-DCMAKE_TOOLCHAIN_FILE=cmake/gnu-tools-for-stm32.cmake`

3. **清除缓存**
   - File → Invalidate Caches / Restart

---

### VS Code: IntelliSense 错误

**解决方案：**

1. **重新生成 compile_commands.json**
   ```bash
   cmake --preset Debug
   ```

2. **检查 `.vscode/c_cpp_properties.json`**
   确保 `compileCommands` 路径正确：
   ```json
   "compileCommands": "${workspaceFolder}/build/Debug/compile_commands.json"
   ```

3. **重新加载 VS Code 窗口**
   - Cmd+Shift+P (macOS) / Ctrl+Shift+P (Windows)
   - 输入 "Developer: Reload Window"

---

## 获取帮助

如果以上方案都无法解决问题：

1. 检查 [CLAUDE.md](CLAUDE.md) 了解项目架构
2. 查看 [CONTRIBUTING.md](CONTRIBUTING.md) 开发规范
3. 参考 STM32F103 数据手册和参考手册
4. 在项目 Issues 中搜索类似问题
5. 提交新的 Issue，附上：
   - 完整的错误信息
   - 操作系统和工具版本
   - 已尝试的解决步骤
