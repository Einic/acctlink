# AcctLink

AcctLink 是一个用 Go 语言编写的高效 Docker 和 Registry 管理工具。它提供了完整的 Docker 环境管理功能，包括自动化安装、配置管理、镜像管理等特性。

## 核心特性

- **自动化部署(离线与在线)**
  - Docker 环境自动安装和配置
  - Docker compose 环境自动安装和配置
  - Docker registry 服务自动部署和管理
  - 系统依赖自动检测和安装

- **配置管理**
  - 基于 YAML 的配置文件
  - 自动生成默认配置
  - 配置验证和错误检查
  - 支持运行时配置更新

- **镜像管理**
  - 自动化编译打包
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

```shell
/data/acctlink
.
├── acctlink_amd64                    # 自动化可执行工具（acctlink_arm64）
├── app
│   ├── Dockerfile              # 项目编译与容器打包文件
│   ├── .env                    # 项目配置.env
│   ├── databases.sql           # 项目数据库SQL文件(配合app reload 自动初始化)
│   └── acctlink-project        # 源码项目目录
│       ├── archive
│       │   ├── openapi
│       ├── package.json
│       ├── packages
│       │   ├── appcontext
│       │   ├── build-cli
│       │   ├── core
│       │   ├── sqlite
│       │   └── wpssync
│       ├── pnpm-lock.yaml
│       ├── pnpm-workspace.yaml
│       ├── tsconfig.json
│       └── turbo.json
├── configs
│   └── config.yaml            # 工具配置文件
├── docker
│   ├── app
│   │   └── logs
│   │       └── app.log  # 业务容器日志
│   ├── mysql
│   │   └── my.cnf       # 数据库配置文件
│   └── nginx
│       ├── conf.d             
│       │   ├── default_443.conf # nginx SSL 配置文件
│       │   ├── default_80.conf  # nginx 配置文件
│       │   └── upstream.conf    # nginx upstream 配置文件
│       ├── logs
│       └── ssl
│           ├── private.pem            # nginx SSL 私钥证书
│           └── public.pem             # nginx SSL 公钥证书
├── docker-compose.yml
├── k8s-deploy.yml                           # k8s deploy 部署文件
└── scripts
    └── logrotate_app.sh                     # 日志分割脚本
```

### 配置

配置文件默认位于 `configs/config.yaml`，系统首次运行时会自动生成默认配置文件。主要配置项包括：

```yaml
docker:
  version: "28.0.0"                                    # Docker 版本
  registry_mirror: "https://d8b3zdiw.mirror.aliyuncs.com"  # 镜像加速器
  insecure_registry: "127.0.0.1:5000"                 # 内部仓库地址
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
  base_url: "https://mirrors.infvie.org/account-docking/" #在线（组件二进制）下载地址
  images:                                                 #指定的镜像名称
   - node-base#20-alpine
   - nginx#1.26-alpine
   - mysql#8.0.37
   - node#20-alpine
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
# 初始化ARCH
ARCH=$(case $(uname -m) in
    x86_64)  echo "amd64" ;;
    aarch64) echo "arm64" ;;
    *) echo "unsupported" ;;
esac)

# 安装基础组件
mkdir -p /data/acctlink/ && \
wget -O /data/acctlink/acctlink_$ARCH https://mirrors.infvie.org/account-docking/acctlink/acctlink_$ARCH && \
cd /data/acctlink/ && \
chmod +x acctlink_$ARCH && \
./acctlink_$ARCH deps install && \
./acctlink_$ARCH install

# 拉起基础组件(mysql/nginx)
./acctlink_$ARCH app create && \
./acctlink_$ARCH app up mysql && \
./acctlink_$ARCH app up nginx && \
./acctlink_$ARCH app ps && \
./acctlink_$ARCH report

# 编译与拉起业务项目
./acctlink_$ARCH app build && \
./acctlink_$ARCH app up app && \
./acctlink_$ARCH app ps

```

```shell
# 调试业务项目，前台运行
./acctlink_$ARCH app compose run app

# 查询基础报告信息状态
./acctlink_$ARCH report

# 初始化业务项目数据库密码
./acctlink_$ARCH app init

# 启动特定服务
./acctlink_$ARCH app up mysql

# 自动导入app下*.sql文件
./acctlink_$ARCH app reload

# 构建特定服务(e.g:app)
./acctlink_$ARCH app build app

# 查看特定服务状态
./acctlink_$ARCH app ps mysql

# 停止特定服务
./acctlink_$ARCH app down mysql

# 执行任意 docker-compose 命令
./acctlink_$ARCH app compose logs mysql
./acctlink_$ARCH app compose exec mysql bash
```

![Image text](https://mirrors.infvie.org/account-docking/acctlink/img/acctlink.png)
#### acctlink-install
![Image text](https://mirrors.infvie.org/account-docking/acctlink/img/acctlink-install.png)
#### acctlink-validating
![Image text](https://mirrors.infvie.org/account-docking/acctlink/img/acctlink-validating.png)
#### acctlink-report
![Image text](https://mirrors.infvie.org/account-docking/acctlink/img/acctlink-report.png)
#### acctlink-uninstall
![Image text](https://mirrors.infvie.org/account-docking/acctlink/img/acctlink-uninstall.png)