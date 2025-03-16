# AcctLink

AcctLink 是一个用 Go 语言编写的高效 Docker 和 Registry 管理工具。它提供了完整的 Docker 环境管理功能，包括自动化安装、配置管理、镜像管理等特性。

## 核心特性

- **自动化部署**
  - Docker 环境自动安装和配置
  - Registry 服务自动部署和管理
  - 系统依赖自动检测和安装

- **配置管理**
  - 基于 YAML 的配置文件
  - 自动生成默认配置
  - 配置验证和错误检查
  - 支持运行时配置更新

- **镜像管理**
  - 私有镜像仓库管理
  - 镜像加载和分发
  - 镜像版本控制

- **系统集成**
  - Systemd 服务集成
  - 日志管理

## 系统要求

- Linux/Unix 系统
- Root 权限
- 支持的架构：amd64, arm64

![Image text](https://mirrors.infvie.org/account-docking/acctlink/acctlink.png)