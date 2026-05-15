---
name: build_oh_small
description: 使用 Docker 构建 OpenHarmony small 系统。该技能提供完整的构建流程，包括环境设置、Docker 配置、依赖安装和镜像生成。当用户需要以下操作时使用：(1) 从源代码构建 OpenHarmony small 系统，(2) 生成用于 QEMU 的 OHOS_Image.bin 和 rootfs_vfat.img，(3) 使用 Docker 设置构建环境，(4) 解决构建依赖和错误
---

# 构建 OpenHarmony Small 系统

## ⚠️ 重要提醒

1. **删除编译产物需管理员权限时**：进入 Docker 容器以 root 删除：
   ```bash
   docker run --rm -v $(pwd):/home/openharmony -w /home/openharmony \
     swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2 \
     rm -rf out/
   ```

2. **问题分析必须具体**：不要猜测原因，通过增加日志或打开日志等级收集更多信息。

3. **记录决策过程**：选择方案后记录到 markdown 文档，记录决策原因和过程。

4. **先最小化验证**：先验证单模块改动，确认有效后再回到完整构建流程。

5. **推荐使用 `build.py` 编译**：`python3 build.py` 是官方推荐方式，稳定性更高。hb 工具对轻量/小型系统支持不如标准系统完善。如使用 hb，增量编译用 `hb build --fast-rebuild`。

6. **⚠️ 使用 hb 时，`hb set` 和 `hb build -f` 会导致增量失效**：请谨慎选择。

## 概述

提供完整的 OpenHarmony small 系统构建流程，用于 QEMU 模拟。使用 Docker 确保环境一致性。

## 前置条件

- 已安装并运行 Docker
- 足够的磁盘空间（至少 10GB）
- OpenHarmony 源代码可用

## 构建流程

### 1. 拉取 Docker 镜像

```bash
docker pull swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2
```

### 2. 使用 build.py 构建

```bash
cd /path/to/ohos_small
docker run -it -v $(pwd):/home/openharmony \
  swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2
```

进入容器后执行编译：

```bash
python3 build.py -p qemu_small_system_demo@ohemu
```

### 3. 验证构建输出

```bash
ls -la out/{device_name}/OHOS_Image.bin
ls -la out/{device_name}/obj/kernel/liteos_a/make_out/rootfs_vfat.img
```

预期产物：
- `OHOS_Image.bin`（约 1.3 MB）- 内核镜像
- `rootfs_vfat.img`（约 2.3 MB）- 根文件系统

## 故障排查

### 缺少 Python 模块

```bash
docker run --rm -v /path/to/ohos_small:/home/openharmony -w /home/openharmony \
  swr.cn-south-1.myhuaweicloud.com/openharmony-docker/docker_oh_small:3.2 \
  /bin/bash -c "python3 -m pip install pyyaml"
```

### 检查构建日志

```bash
cat out/{device_name}/build.log
cat out/{device_name}/error.log
```

## 构建产物

详见 [build-artifacts.md](references/build-artifacts.md)

## 下一步

成功构建后，使用 `run_oh_qemu` 技能在 QEMU 模拟中运行系统。
