# CLion 配置说明

本文档说明如何在 CLion 中正确配置 STM32 项目。

## 问题

CLion 默认不使用 CMake Presets，会导致以下问题：
1. 使用系统编译器（AppleClang）而非 ARM 交叉编译器
2. 缺少必要的 CMSIS 配置变量

## 解决方案

### 方法 1：配置 CMake Toolchain（推荐）

1. **打开 Settings/Preferences**
   - macOS: `CLion → Settings` (⌘,)
   - Windows/Linux: `File → Settings` (Ctrl+Alt+S)

2. **导航到 Build, Execution, Deployment → CMake**

3. **配置 Debug Profile**
   - Build type: `Debug`
   - CMake options:
     ```
     -DCMAKE_TOOLCHAIN_FILE=cmake/gnu-tools-for-stm32.cmake
     ```
   - Generator: `Ninja`

4. **配置 Release Profile**
   - Build type: `Release`
   - CMake options:
     ```
     -DCMAKE_TOOLCHAIN_FILE=cmake/gnu-tools-for-stm32.cmake
     ```
   - Generator: `Ninja`

5. **应用并重新加载 CMake 项目**

### 方法 2：使用 CMake Presets（CLion 2021.3+）

1. **打开 Settings/Preferences → Build, Execution, Deployment → CMake**

2. **启用 CMake Presets**
   - 选中 "Enable CMake Presets"
   - 选中 "Use CMake Presets from CMakePresets.json"

3. **CLion 会自动识别项目中的 Debug 和 Release presets**

4. **重新加载 CMake 项目**

## 验证配置

配置完成后，在 CMake 窗口中应该看到：

```
-- The C compiler identification is GNU 14.3.1
-- The CXX compiler identification is GNU 14.3.1
...
-- Found assembler: /opt/homebrew/bin/arm-none-eabi-gcc
```

**✅ 正确**: 使用 GNU (arm-none-eabi-gcc)
**❌ 错误**: 使用 AppleClang 或系统 GCC

## 常见问题

### Q: 编译失败，提示找不到 arm-none-eabi-gcc

**A:** 确保已安装 ARM 工具链：
```bash
# 检查工具链是否安装
which arm-none-eabi-gcc

# macOS 使用 Homebrew 安装
brew install --cask gcc-arm-embedded

# 或通过 STM32CubeIDE 安装
```

### Q: CMake 提示 "set_target_properties called with incorrect number of arguments"

**A:** 这个问题已修复。确保 `CMakeLists.txt` 中包含默认变量设置：
```cmake
# Set default PROJECT_ORIGINAL_NAME if not provided by preset
if(NOT DEFINED PROJECT_ORIGINAL_NAME)
    set(PROJECT_ORIGINAL_NAME "${PROJECT_NAME}")
endif()
```

### Q: 如何切换 Debug/Release 构建？

**A:** 在 CLion 顶部工具栏的构建配置下拉菜单中选择对应的 profile。

## 调试配置

创建 Embedded GDB Server 配置用于硬件调试：

1. **Run → Edit Configurations**
2. **添加 Embedded GDB Server**
3. **配置参数**：
   - Target: `test32`
   - Executable: `cmake-build-debug/test32.elf`
   - GDB: `/opt/homebrew/bin/arm-none-eabi-gdb`
   - 'target remote' args: `localhost:3333` (ST-Link GDB server)

## 烧录固件

从终端使用 st-flash：
```bash
# Debug 构建
st-flash write cmake-build-debug/test32.bin 0x8000000

# Release 构建
st-flash write cmake-build-release/test32.bin 0x8000000
```

或使用 STM32CubeProgrammer GUI。
