# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2025-12-05

### Added
- Initial release of STM32F103C8T6 development template
- CMake build system with Debug/Release presets
- Support for macOS M1/M2/M3, Windows, and Linux
- VS Code complete configuration (debug, tasks, IntelliSense)
- CLion IDE configuration guide
- Comprehensive documentation in Chinese:
  - Quick start guide (README.md)
  - Architecture documentation (CLAUDE.md)
  - Contributing guidelines with Angular Commit Style (CONTRIBUTING.md)
  - Code examples (GPIO, UART, Timer, etc.)
  - Troubleshooting guide
  - Chinese datasheet acquisition guide
- Startup code and basic system files
- Linker script for STM32F103C8T6
- OpenOCD and ST-Link debugging configurations
- EditorConfig for consistent code style
- Comprehensive .gitignore configuration

### Features
- Cross-platform development support
- CMake Presets for easy configuration
- Automatic .hex and .bin generation
- Memory usage display during build
- C11 and C++20 standard support
- Float printf support enabled
- Memory section optimization enabled

### Documentation
- 10+ markdown documentation files
- Complete setup guide for macOS and Windows
- Hardware debugging configuration
- Serial debugging instructions
- Common GDB commands reference
- Document organization best practices

[Unreleased]: https://github.com/martinadamsdev/STM32F103C8T6-Scaffold/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/martinadamsdev/STM32F103C8T6-Scaffold/releases/tag/v1.0.0
