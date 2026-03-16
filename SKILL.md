---
name: build_oh_small
description: 使用 Docker 构建 OpenHarmony small 系统。该技能提供完整的构建流程，包括环境设置、Docker 配置、依赖安装和镜像生成。当用户需要以下操作时使用：(1) 从源代码构建 OpenHarmony small 系统，(2) 生成用于 QEMU 的 OHOS_Image.bin 和 rootfs_vfat.img，(3) 使用 Docker 设置构建环境，(4) 解决构建依赖和错误
---

# 构建 OpenHarmony Small 系统

## 概述

该技能提供完整的 OpenHarmony small 系统构建流程，用于 QEMU 模拟。构建过程使用 Docker 确保环境一致性，并生成必要的系统镜像。

## 前置条件

- 已安装并运行 Docker
- 足够的磁盘空间（至少 10GB）
- 互联网连接，用于下载 Docker 镜像和依赖
- OpenHarmony 源代码可用

## 构建流程

### 步骤 1：验证环境

检查 Docker 安装：

```bash
docker --version
```

验证 QEMU 是否可用（用于运行构建的系统）：

```bash
qemu-system-arm --version
```

### 步骤 2：拉取 Docker 镜像

Docker镜像构建环境的mapping关系：

- 小型系统 - small - swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2
- 轻量系统 - mini - swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_mini:3.2

使用官方 OpenHarmony small 系统 Docker 镜像：

```bash
docker pull swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2
```

### 步骤 3：使用 Docker 构建系统

导航到 OpenHarmony 源代码目录。

进入小型系统Docker构建环境：

```bash
docker run -it -v $(pwd):/home/openharmony swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2
```

进入轻量系统Docker构建环境：

```bash
docker run -it -v $(pwd):/home/openharmony swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_mini:3.2
```

然后启动编译脚本：

```bash
python3 build.py -p {product_name}@{company}
```

其中，{product_name}为当前版本支持的平台，{company}为{product_name}对应的公司名。

举个例子，如果您要编译的产品为hisilicon下的ipcamera_hispark_taurus，您可以输入以下命令来启动编译：

```bash
python3 build.py -p ipcamera_hispark_taurus@hisilicon
```
同样，如果您要编译的产品是ohemu下的qemu_small_system_demo，那么您可以输入以下命令来启动编译：

```bash
python3 build.py -p qemu_small_system_demo@ohemu
```

### 步骤 4：验证构建输出

查看编译结果：在编译结束后，编译所生成的文件都会被存放在 `out/{device_name}/` 目录下。

检查是否生成了以下文件：

```bash
ls -la out/{device_name}/OHOS_Image.bin
ls -la out/{device_name}/obj/kernel/liteos_a/make_out/rootfs_vfat.img
```

预期输出：

- `OHOS_Image.bin`（约 1.3 MB）- 系统内核镜像
- `rootfs_vfat.img`（约 2.3 MB）- 根文件系统

## 故障排查

### 缺少 Python 模块

如果遇到 `ModuleNotFoundError: No module named 'yaml'`：

```bash
docker run --rm -v /path/to/ohos_small:/home/openharmony -w /home/openharmony \
  swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2 \
  /bin/bash -c "python3 -m pip install pyyaml"
```

### 构建错误

检查构建日志以获取详细错误信息：

```bash
cat out/{device_name}/build.log
```

检查错误日志：

```bash
cat out/{device_name}/error.log
```

**注意**：不需要使用hb工具来编译；hb 工具在早期对轻量/小型系统的支持不如标准系统完善，因此官方文档在针对这两类系统时，有时会保留直接调用 build.py 的说明以确保稳定性。

## 构建产物

构建过程生成以下关键产物：

### 内核镜像

| 文件 | 路径 | 大小 | 说明 |
|------|------|------|------|
| OHOS_Image.bin | `out/{device_name}/OHOS_Image.bin` | ~1.3 MB | 内核镜像（用于QEMU启动） |
| OHOS_Image | `out/{device_name}/OHOS_Image` | ~1.7 MB | ELF格式内核（带符号信息） |
| OHOS_Image.map | `out/{device_name}/OHOS_Image.map` | ~952 KB | 内存映射文件 |

### 根文件系统镜像

| 文件 | 路径 | 大小 | 说明 |
|------|------|------|------|
| rootfs_vfat.img | `out/{device_name}/obj/kernel/liteos_a/make_out/rootfs_vfat.img` | ~2.3 MB | 根文件系统镜像（VFAT格式） |

### 用户态可执行程序

位于 `out/{device_name}/bin/` 目录：

| 程序 | 大小 | 说明 |
|------|------|------|
| init | ~175 KB | 系统初始化进程 |
| shell | ~22 KB | 命令行Shell |
| mksh | ~172 KB | MirBSD Korn Shell |
| toybox | ~79 KB | 常用工具集 |
| softbus_server | ~3.3 KB | 软总线服务 |
| deviceauth_service | ~658 KB | 设备认证服务 |
| huks_server | ~96 KB | HUKS密钥服务 |
| wms_server | ~32 KB | 窗口管理服务 |

### 动态库

位于 `out/{device_name}/usr/lib/` 目录，主要库包括：

| 库文件 | 大小 | 说明 |
|--------|------|------|
| libsoftbus_server_frame.so | ~1.5 MB | 软总线框架 |
| libjerry-core_shared.so | ~499 KB | JerryScript引擎 |
| libfreetype.so | ~590 KB | 字体渲染库 |
| libpng.so | ~197 KB | PNG图像库 |
| libjpeg.so | ~259 KB | JPEG图像库 |
| libui.so | ~56 KB | UI库 |
| libopenssl_shared.so | ~85 KB | OpenSSL |
| libbegetutil.so | ~96 KB | 启动工具库 |

### 系统配置文件

```
rootfs/
├── etc/
│   ├── init.cfg                    # 启动配置
│   └── softbus_trans_permission.json # 软总线权限配置
├── system/etc/param/
│   ├── ohos.para                   # 系统参数
│   └── ohos.para.dac               # 参数权限配置
└── vendor/etc/param/
    └── vendor.para                 # 厂商参数
```

### 构建日志

| 文件 | 路径 | 说明 |
|------|------|------|
| build.log | `out/{device_name}/build.log` | 完整构建日志 |

## 下一步

成功构建后，使用 `run_oh_qemu` 技能在 QEMU 模拟中运行系统。
