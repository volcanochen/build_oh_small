# 构建产物详解

## 内核镜像

| 文件 | 路径 | 大小 | 说明 |
|------|------|------|------|
| OHOS_Image.bin | `out/{device_name}/OHOS_Image.bin` | ~1.3 MB | 内核镜像（用于QEMU启动） |
| OHOS_Image | `out/{device_name}/OHOS_Image` | ~1.7 MB | ELF格式内核（带符号信息） |
| OHOS_Image.map | `out/{device_name}/OHOS_Image.map` | ~952 KB | 内存映射文件 |

## 根文件系统镜像

| 文件 | 路径 | 大小 | 说明 |
|------|------|------|------|
| rootfs_vfat.img | `out/{device_name}/obj/kernel/liteos_a/make_out/rootfs_vfat.img` | ~2.3 MB | 根文件系统镜像（VFAT格式） |

## 用户态可执行程序

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

## 动态库

位于 `out/{device_name}/usr/lib/` 目录：

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

## 系统配置文件

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

## 构建日志

| 文件 | 路径 | 说明 |
|------|------|------|
| build.log | `out/{device_name}/build.log` | 完整构建日志 |
