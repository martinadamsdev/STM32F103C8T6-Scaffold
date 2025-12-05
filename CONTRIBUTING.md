# 协同开发指南

本文档说明如何在此 STM32 项目中进行协同开发。

## 环境搭建

### 必需工具

1. **STM32CubeIDE VS Code Extension**
   - 从 VS Code 扩展市场安装 STM32 Cube IDE
   - 会自动安装 GNU Tools for STM32 工具链

2. **构建工具**
   - CMake 3.20+
   - Ninja 构建系统
   - arm-none-eabi-gcc 工具链（通过 STM32Cube 扩展安装）

3. **代码提示和补全**
   - clangd（通过 STM32Cube 扩展的 cube-clangd）
   - 配置已包含在 `.vscode/settings.json` 和 `.clangd` 中

### 初始化步骤

```bash
# 1. 克隆仓库
git clone <repository-url>
cd test32

# 2. 配置项目（Debug 模式）
cmake --preset Debug

# 3. 构建项目验证环境
cmake --build --preset Debug

# 4. 检查构建输出
ls -l build/Debug/*.elf build/Debug/*.hex build/Debug/*.bin
```

## 开发工作流

### 分支策略

建议使用以下分支模型：

- `main` - 稳定版本，只接受经过测试的代码
- `develop` - 开发分支，集成最新功能
- `feature/<功能名>` - 功能开发分支
- `fix/<问题描述>` - 问题修复分支

示例：
```bash
# 创建功能分支
git checkout -b feature/uart-driver develop

# 创建修复分支
git checkout -b fix/timer-overflow develop
```

### 代码修改流程

1. **创建功能分支**
   ```bash
   git checkout -b feature/your-feature develop
   ```

2. **添加源文件**

   在 `Src/` 目录创建 `.c` 文件，在 `Inc/` 目录创建 `.h` 文件：
   ```bash
   # 示例：添加 UART 驱动
   touch Src/uart_driver.c
   touch Inc/uart_driver.h
   ```

3. **更新 CMakeLists.txt**

   编辑 `CMakeLists.txt`，添加新源文件：
   ```cmake
   set(sources_SRCS
       ${CMAKE_CURRENT_SOURCE_DIR}/Src/main.c
       ${CMAKE_CURRENT_SOURCE_DIR}/Src/uart_driver.c
   )
   ```

4. **重新配置和构建**
   ```bash
   cmake --preset Debug
   cmake --build --preset Debug
   ```

5. **验证构建**

   确保没有编译错误和警告：
   ```bash
   # 检查编译输出
   # 查看内存使用情况（构建时会自动显示）
   ```

6. **提交更改**
   ```bash
   git add Src/uart_driver.c Inc/uart_driver.h CMakeLists.txt
   git commit -m "feat: 添加 UART 驱动模块"
   ```

### 提交信息规范

使用 Angular Commit Message Format：

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### 格式说明

**Header（必需）**
- `type`：提交类型（必需）
- `scope`：影响范围（可选）
- `subject`：简短描述（必需）

**Body（可选）**
- 详细说明本次提交的动机和变更内容
- 与之前行为的对比

**Footer（可选）**
- Breaking Changes：不兼容的变更
- 关闭的 Issue：`Closes #123, #456`

#### Type 类型

- `feat`: 新功能（feature）
- `fix`: 修复 bug
- `docs`: 仅文档变更
- `style`: 代码格式调整（不影响代码运行的变动，如空格、格式化等）
- `refactor`: 重构（既不是新增功能，也不是修复 bug 的代码变动）
- `perf`: 性能优化
- `test`: 添加或修改测试
- `build`: 影响构建系统或外部依赖的变更（如 CMake、工具链配置）
- `ci`: 持续集成配置文件和脚本的变更
- `chore`: 其他不修改源代码或测试文件的变更
- `revert`: 回退之前的提交

#### Scope 范围

Scope 用于说明提交影响的范围，例如：

- `uart`: UART 驱动相关
- `gpio`: GPIO 相关
- `timer`: 定时器相关
- `i2c`: I2C 通信相关
- `spi`: SPI 通信相关
- `adc`: ADC 相关
- `build`: 构建系统相关
- `linker`: 链接脚本相关
- `*`: 影响多个范围

#### Subject 主题

- 使用祈使句，现在时态："change" 而非 "changed" 或 "changes"
- 首字母小写
- 结尾不加句号
- 简洁明了，不超过 50 个字符

#### 示例

**简单提交：**
```bash
git commit -m "feat(uart): 实现 UART 驱动基础功能"
git commit -m "fix(timer): 修复定时器溢出中断处理错误"
git commit -m "docs(readme): 更新快速开始指南"
git commit -m "style(gpio): 统一 GPIO 代码格式"
git commit -m "refactor(i2c): 简化 I2C 初始化流程"
git commit -m "perf(adc): 优化 ADC 采样速度"
git commit -m "build(cmake): 添加 Release 构建优化选项"
```

**包含 Body 的提交：**
```bash
git commit -m "feat(uart): 添加 DMA 传输支持

实现了基于 DMA 的 UART 数据传输，减少 CPU 占用。
主要变更：
- 添加 DMA 初始化函数
- 实现 DMA 中断处理
- 更新 UART 发送接收接口"
```

**包含 Breaking Change：**
```bash
git commit -m "refactor(uart): 重构 UART API 接口

将 UART 初始化接口从 uart_init() 改为 uart_config()，
参数结构体也相应调整。

BREAKING CHANGE: uart_init() 已被移除，
请使用 uart_config() 并传入新的配置结构体 uart_config_t"
```

**关联 Issue：**
```bash
git commit -m "fix(timer): 修复 TIM2 中断优先级问题

调整 TIM2 中断优先级，避免与 UART 中断冲突。

Closes #42"
```

**Revert 提交：**
```bash
git commit -m "revert: feat(spi): 添加 SPI DMA 支持

This reverts commit a1b2c3d4e5f6.
原因：发现 DMA 配置导致系统不稳定，需要进一步调试"
```

## 代码规范

### C 代码风格

1. **命名规范**
   - 函数名：`snake_case`，如 `gpio_init()`, `uart_send_data()`
   - 变量名：`snake_case`，如 `byte_count`, `timer_value`
   - 宏定义：`UPPER_CASE`，如 `MAX_BUFFER_SIZE`, `GPIO_PIN_5`
   - 类型定义：`snake_case_t`，如 `uart_config_t`

2. **文件组织**
   ```c
   // 头文件保护
   #ifndef UART_DRIVER_H
   #define UART_DRIVER_H

   // 包含必要的头文件
   #include <stdint.h>
   #include <stdbool.h>

   // 宏定义
   #define UART_BUFFER_SIZE 256

   // 类型定义
   typedef struct {
       uint32_t baudrate;
       uint8_t data_bits;
   } uart_config_t;

   // 函数声明
   void uart_init(uart_config_t *config);

   #endif // UART_DRIVER_H
   ```

3. **注释要求**
   - 每个函数需要简要说明其功能
   - 复杂逻辑需要添加行内注释
   - 寄存器操作需要说明目的

### 内存和资源限制

STM32F103C8T6 资源有限，开发时注意：

- **Flash**: 64KB（需要注意代码大小）
- **RAM**: 20KB（静态变量和栈需要谨慎使用）
- **堆栈**: 最小堆 512 字节，最小栈 1024 字节

**最佳实践：**
```c
// ✅ 推荐：使用栈分配小缓冲区
void process_data(void) {
    uint8_t buffer[32];
    // ...
}

// ❌ 避免：大数组静态分配
static uint8_t large_buffer[10240]; // 会耗尽 RAM

// ✅ 推荐：使用 const 将只读数据放在 Flash
const uint8_t lookup_table[256] = { /* ... */ };

// ✅ 推荐：检查构建后的内存使用
// 构建时会显示：
// Memory region         Used Size  Region Size  %age Used
//            FLASH:       1024 B        64 KB      1.56%
//              RAM:        512 B        20 KB      2.50%
```

## 调试和测试

### 构建配置

```bash
# Debug 构建（包含调试符号，未优化）
cmake --preset Debug
cmake --build --preset Debug

# Release 构建（优化体积，无调试符号）
cmake --preset Release
cmake --build --preset Release
```

### 检查构建输出

每次构建后检查：
```bash
# 查看内存使用（自动显示）
# 检查 .map 文件了解内存分布
cat build/Debug/test32.map | grep "Memory region"

# 检查符号大小
arm-none-eabi-nm -S --size-sort build/Debug/test32.elf | tail -20
```

## 合并请求流程

1. **确保代码能够构建**
   ```bash
   cmake --preset Debug
   cmake --build --preset Debug
   cmake --preset Release
   cmake --build --preset Release
   ```

2. **检查无编译警告**
   - 项目已启用 `-Wall -Wextra -Wpedantic`
   - 必须解决所有警告

3. **创建合并请求**
   - 提供清晰的功能描述
   - 说明测试方法（如果在硬件上测试过，注明测试条件）
   - 附上内存使用情况

4. **代码审查**
   - 至少一位团队成员审查
   - 检查是否符合代码规范
   - 验证内存使用合理

## 常见问题

### 如何添加外部库？

编辑 `CMakeLists.txt`：

```cmake
# 添加库的包含目录
set(include_DIRS
    ${CMAKE_CURRENT_SOURCE_DIR}/Inc
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/third_party/include
)

# 添加库的源文件
set(sources_SRCS
    ${CMAKE_CURRENT_SOURCE_DIR}/Src/main.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/third_party/src/library.c
)

# 如果是预编译库
set(link_DIRS
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/third_party
)
set(link_LIBS
    third_party_lib
)
```

### 如何添加编译宏定义？

编辑 `CMakeLists.txt`：

```cmake
set(symbols_SYMB
    USE_FULL_ASSERT
    DEBUG_UART_ENABLED
)
```

### 如何修改链接脚本？

链接脚本 `stm32f103x8_flash.ld` 定义了内存布局。修改时需要：

1. 了解 STM32F103x8 的内存限制（64KB Flash, 20KB RAM）
2. 修改后重新构建
3. 检查 `.map` 文件确认内存分配正确

## 项目维护

### 不要修改的文件

- `cmake/vscode_generated.cmake` - STM32CubeIDE 自动生成
- `Src/startup_stm32f103xx.S` - 启动文件（除非必要）
- `Src/syscall.c`, `Src/sysmem.c` - 系统调用实现（除非必要）

### 可以修改的文件

- `CMakeLists.txt` - 添加源文件、库、编译选项
- `Src/main.c` - 主应用程序
- 所有用户创建的 `.c` 和 `.h` 文件

## 获取帮助

- 查看 `CLAUDE.md` 了解项目架构
- 参考 STM32F103 参考手册和数据手册
- 检查构建输出的错误信息
