# 文档组织结构说明

本文档说明此项目的文档组织遵循的开源项目最佳实践。

## 📁 文档分类原则

### 根目录文档（项目根）

**原则：** 放置用户首次接触项目时最需要的文档，以及工具需要的配置文件

- **README.md** ✅
  - 项目首页和入口
  - 快速开始指南
  - 环境搭建（macOS 和 Windows）
  - 基本构建和烧录说明
  - 简要的调试指南
  - 文档导航链接

- **CLAUDE.md** ✅
  - **必须在根目录**（Claude Code 会在根目录查找）
  - 项目架构和构建系统详解
  - 供 Claude Code 使用的配置和上下文信息
  - 虽然是技术文档，但因工具要求必须放在根目录

- **LICENSE** ✅
  - 软件许可证
  - 必须在根目录（GitHub 等平台会自动识别）

- **CONTRIBUTING.md** ✅
  - 贡献指南
  - 协同开发规范
  - Git 提交格式（Angular Style）
  - 代码审查流程
  - 必须在根目录（开源项目标准位置）

- **配置文件** ✅
  - `.gitignore`
  - `.editorconfig`
  - `.clangd`
  - 等等

### docs/ 目录文档

**原则：** 放置深入的、详细的、技术性的文档

#### 架构和设计文档

- **CLAUDE.md**（位于根目录）✅
  - 项目架构详解
  - CMake 构建系统原理
  - 内存布局
  - 启动流程
  - 文件作用详细说明
  - **特殊说明：** 虽然是技术详细文档，但必须放在根目录供 Claude Code 使用
  - 适合：已经入门的开发者深入了解，以及 AI 辅助编程工具

#### 教程和示例

- **docs/EXAMPLES.md** ✅
  - 详细的代码示例
  - GPIO、UART、定时器等外设使用
  - 完整的代码清单
  - 硬件连接说明
  - 适合：学习如何开发具体功能

#### 工具和配置

- **docs/clion-setup.md** ✅
  - 特定 IDE 的详细配置
  - 截图和步骤说明
  - 适合：CLion 用户

- **docs/setup-macos.sh** ✅
  - 自动化脚本
  - 减少手动操作
  - 适合：macOS 用户快速配置

#### 运维和故障排除

- **docs/TROUBLESHOOTING.md** ✅
  - 常见问题和解决方案
  - 详细的错误信息和修复步骤
  - 分类清晰的问题列表
  - 适合：遇到问题时查阅

#### 项目管理

- **docs/PROJECT_SUMMARY.md** ✅
  - 项目完成状态
  - 功能清单
  - 验证步骤
  - 适合：项目维护者和管理者

- **docs/README.md** ✅
  - docs 目录索引
  - 文档导航
  - 阅读建议

## 🎯 为什么这样组织？

### 符合开源标准

大多数知名开源项目采用类似结构：

```
project/
├── README.md          # 项目入口
├── LICENSE            # 许可证
├── CONTRIBUTING.md    # 贡献指南
├── docs/              # 详细文档
│   ├── README.md      # 文档索引
│   ├── architecture.md
│   ├── api.md
│   └── ...
├── examples/          # 示例代码（可选）
└── src/               # 源代码
```

**参考项目：**
- [TypeScript](https://github.com/microsoft/TypeScript)
- [Kubernetes](https://github.com/kubernetes/kubernetes)
- [React](https://github.com/facebook/react)

### 用户体验优化

1. **新用户友好**
   - README.md 提供快速开始
   - 不被过多文档淹没
   - 清晰的下一步指引

2. **深入学习者**
   - docs/ 提供详细资料
   - 分类清晰，易于查找
   - 相互引用，形成知识网络

3. **维护者便利**
   - 文档职责明确
   - 更新不影响主要文件
   - 便于版本控制

### GitHub/GitLab 集成

- `README.md` 自动显示在仓库首页
- `LICENSE` 被自动识别显示许可证
- `CONTRIBUTING.md` 在 PR 页面自动提示
- `docs/` 可生成 GitHub Pages

## 📝 文档编写指南

### README.md 编写要点

**应该包含：**
- ✅ 项目简介（一句话说明）
- ✅ 硬件规格
- ✅ 快速开始（15 分钟内能运行）
- ✅ 构建命令（复制粘贴即用）
- ✅ 基本调试方法
- ✅ 文档链接（指向 docs/）

**不应该包含：**
- ❌ 详细的架构说明（放 docs/CLAUDE.md）
- ❌ 完整的代码示例（放 docs/EXAMPLES.md）
- ❌ 详细的故障排除（放 docs/TROUBLESHOOTING.md）
- ❌ 特定 IDE 配置细节（放 docs/clion-setup.md）

### docs/ 文档编写要点

**命名规范：**
- 使用大写的描述性名称：`EXAMPLES.md`，`TROUBLESHOOTING.md`
- IDE/工具相关用小写：`clion-setup.md`
- 脚本文件：`setup-macos.sh`

**内容组织：**
- 每个文档只关注一个主题
- 使用清晰的标题层次
- 提供目录（对于长文档）
- 适当的代码示例
- 相互引用其他文档

**Markdown 风格：**
- 使用代码块标记语言
- 使用表情符号增强可读性（适度）
- 添加注释说明复杂命令
- 提供多种操作系统的示例

## 🔄 文档维护

### 何时更新文档

- **README.md**
  - 添加新的主要功能
  - 修改构建流程
  - 更新硬件要求

- **CONTRIBUTING.md**
  - 修改开发规范
  - 更新提交格式
  - 调整审查流程

- **docs/**
  - 添加新示例代码
  - 发现新的常见问题
  - IDE 工具更新
  - 架构重大变更

### 文档同步

更新代码时，确保同步更新：

1. 修改 API → 更新 docs/CLAUDE.md
2. 添加示例 → 更新 docs/EXAMPLES.md
3. 发现 Bug → 更新 docs/TROUBLESHOOTING.md
4. 更改构建 → 更新 README.md

### 版本控制

- 文档随代码一起版本控制
- 使用 Git 提交规范
  - `docs(readme): 更新快速开始指南`
  - `docs(examples): 添加 I2C 示例`
- 重大文档更新单独 PR

## ✅ 检查清单

新项目或重大更新时检查：

- [ ] README.md 是否简洁明了？
- [ ] 新用户能否在 15 分钟内运行项目？
- [ ] LICENSE 和 CONTRIBUTING.md 在根目录？
- [ ] docs/ 目录有 README.md 索引？
- [ ] 所有文档链接正确？
- [ ] 文档内容与代码同步？
- [ ] 代码示例能够编译运行？
- [ ] 截图和命令是最新的？

## 🌐 多语言支持（未来）

如果需要支持多语言文档：

```
docs/
├── README.md          # 英文索引
├── zh-CN/             # 简体中文
│   ├── README.md
│   ├── CLAUDE.md
│   └── ...
├── ja-JP/             # 日文
│   └── ...
└── en/                # 英文（或直接在 docs/ 根）
    └── ...
```

当前项目使用简体中文作为主要语言。

## 📚 参考资源

### 文档最佳实践

- [Write the Docs](https://www.writethedocs.org/)
- [Google Developer Documentation Style Guide](https://developers.google.com/style)
- [Microsoft Writing Style Guide](https://docs.microsoft.com/en-us/style-guide/)

### Markdown 指南

- [GitHub Flavored Markdown](https://github.github.com/gfm/)
- [Markdown Guide](https://www.markdownguide.org/)

### 开源项目参考

- [Awesome README](https://github.com/matiassingers/awesome-readme)
- [Standard Readme](https://github.com/RichardLitt/standard-readme)

---

**本文档版本：** 1.0
**最后更新：** 2025-12-05
