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

## 快速开始
- create自动初始化

acctlink/
├── account-docking.yaml
├── acctlink_amd64
├── app
│   ├── Dockerfile
│   └── project-demo
├── configs
│   └── config.yaml
├── docker
│   ├── mysql
│   │   └── my.cnf
│   └── nginx
│       └── conf.d
│           └── default.conf
└── docker-compose.yml
### 配置

配置文件默认位于 `configs/config.yaml`，系统首次运行时会自动生成默认配置文件。主要配置项包括：

```yaml
docker:
  version: "28.0.0"                                    # Docker 版本
  registry_mirror: "https://d8b3zdiw.mirror.aliyuncs.com"  # 镜像加速器
  insecure_registry: "127.0.0.1:5000"                 # 不安全仓库地址
  data_root: "/data/docker"                           # Docker 数据目录
  bin_root: "/usr/local/bin"                          # Docker 二进制文件目录
  systemd_service: "docker"                           # Docker 服务名称
  config_path: "/etc/docker"                          # Docker 配置目录
registry:
  port: 5000                                          # Registry 端口
  data_path: "/data/docker-registry"                  # Registry 数据目录
  bin_path: "/usr/local/bin/registry"                 # Registry 二进制文件路径
  config_path: "/etc/docker/registry"                 # Registry 配置目录
  systemd_service: "registry"                         # Registry 服务名称
download:
  base_url: "https://mirrors.infvie.org/account-docking/" #在线（镜像、组件二进制）下载地址
  images:                                                 #指定的镜像名称
    - images/alpine#3.19.1                                
    - images/mysql#8.0.37
    - images/nginx#1.26-alpine-unpri
    - images/node#20-alpine
  images_path: "images"                                   # 本地目录
system:
  required_deps:                                      # 必需的系统依赖
    - tar
    - unzip
    - git
    - wget
    - curl
  optional_deps:                                      # 可选的系统依赖
    - vim
```

### 使用方法

```shell
# 安装基础组件
wget https://mirrors.infvie.org/account-docking/acctlink/acctlink_amd64 && chmod +x acctlink_amd64 && ./acctlink_amd64 deps && ./acctlink_amd64 install

# 拉起基础组件(mysql/nginx)
./acctlink_amd64 app create && ./acctlink_amd64 app up mysql && ./acctlink_amd64 app up nginx && ./acctlink_amd64 app ps

# 编译与拉起业务项目
./acctlink_amd64 app build && ./acctlink_amd64 app up app && ./acctlink_amd64 app ps

```

```shell
# 调试业务项目，前台运行
./acctlink_amd64 app compose run app

# 初始化业务项目数据库密码
./acctlink_amd64 app init

# 启动特定服务
acctlink app up mysql

# 构建特定服务
acctlink app build nginx

# 查看特定服务状态
acctlink app ps mysql

# 停止特定服务
acctlink app down mysql

# 执行任意 docker-compose 命令
acctlink app compose logs mysql
acctlink app compose exec mysql bash
```