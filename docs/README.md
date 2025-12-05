# 文档目录

本目录包含 STM32F103C8T6 项目的详细文档。

## 📚 文档导航

### 🏗️ 架构和开发

- **[CLAUDE.md](../CLAUDE.md)** - 项目架构和构建系统详解（位于根目录）
  - CMake 构建流程
  - 内存布局
  - 启动流程
  - 文件作用说明
  - 开发工作流
  - **注意：** 此文件在项目根目录，供 Claude Code 使用

- **[DATASHEETS.md](DATASHEETS.md)** - 数据手册和参考资料指南
  - STM32F103 中文数据手册获取
  - STM32F103 中文参考手册
  - Cortex-M3 中文技术手册
  - 官方和社区资源链接
  - 推荐学习路径

- **[EXAMPLES.md](EXAMPLES.md)** - 代码示例
  - GPIO 输出（LED 闪烁）
  - GPIO 输入（按钮检测）
  - SysTick 延时函数
  - UART 串口通信
  - 定时器中断

### 🔧 配置和工具

- **[clion-setup.md](clion-setup.md)** - CLion IDE 配置指南
  - CMake Toolchain 配置
  - CMake Presets 使用
  - 调试配置
  - 常见问题解决

### 🐛 故障排除

- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - 常见问题和解决方案
  - 构建问题
  - 烧录问题
  - 调试问题
  - 内存问题
  - IDE 特定问题

### 📊 项目信息

- **[PROJECT_SUMMARY.md](PROJECT_SUMMARY.md)** - 项目完整总结
  - 文件清单
  - 功能特性
  - 已修复的问题
  - 适用场景
  - 验证清单

## 🚀 快速开始

如果你是第一次使用本项目，建议按以下顺序阅读：

1. **[../README.md](../README.md)** - 项目首页，了解基本信息和快速开始
2. **[../CLAUDE.md](../CLAUDE.md)** - 理解项目架构和构建系统
3. **[EXAMPLES.md](EXAMPLES.md)** - 学习示例代码
4. **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - 遇到问题时查阅

## 🛠️ 开发者资源

### macOS 开发者
- 参考 [../README.md](../README.md) 的 macOS 章节
- 按照步骤安装 Homebrew、ARM 工具链、CMake 等

### Windows 开发者
- 参考 [../README.md](../README.md) 的 Windows 章节
- 查看 [TROUBLESHOOTING.md](TROUBLESHOOTING.md) 解决常见问题

### CLion 用户
- 查看 [clion-setup.md](clion-setup.md)
- 配置 CMake Toolchain 和调试器

## 📝 贡献文档

如果你发现文档有错误或需要补充：

1. 参考 [../CONTRIBUTING.md](../CONTRIBUTING.md) 了解贡献流程
2. 使用 Angular Commit Style 提交
3. 更新相关的文档索引

## 🔗 外部资源

### 官方文档
- [STM32F103 参考手册](https://www.st.com/resource/en/reference_manual/cd00171190-stm32f101xx-stm32f102xx-stm32f103xx-stm32f105xx-and-stm32f107xx-advanced-arm-based-32-bit-mcus-stmicroelectronics.pdf)
- [STM32F103 数据手册](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
- [ARM Cortex-M3 技术手册](https://developer.arm.com/documentation/ddi0337/latest/)

### 开发工具
- [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html)
- [OpenOCD 文档](http://openocd.org/doc/)

### 社区资源
- [STM32 官方论坛](https://community.st.com/)
- [ARM Mbed](https://os.mbed.com/)
- [Stack Overflow - STM32](https://stackoverflow.com/questions/tagged/stm32)

---

**文档版本：** 1.0
**最后更新：** 2025-12-05
**维护者：** 项目团队
