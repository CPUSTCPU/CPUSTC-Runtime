# CPUSTC Runtime

CPUSTC-Runtime 保存 CPUSTC LA32R 系统使用的预构建运行时组件。当前内容面向
`loongarch32r` 架构和 `ilp32s` 软浮点 ABI，不能直接在 x86_64 主机上运行。

## 组件

| Bundle | 版本 | 主要命令或内容 |
| --- | --- | --- |
| `cpython` | 3.11.2 | `python3`、`pip3`、CPython 标准库 |
| `native-gcc` | 8.3.0 | LA32R 原生 GCC、binutils 和 `/opt/native-sysroot` |
| `w3m` | 0.5.3+git20230718 | 终端网页浏览器 |
| `hax` | 0.3.0-22-gb1d2690 | 命令行编码助手 |
| `noto-cjk` | backup-20260829 | Noto Sans CJK 字体 |
| `proxy` | 1 | CPUSTC USB 网络代理配置命令 |

每个 bundle 均采用以下目录结构：

```text
bundles/<id>/
|-- manifest.yaml   # 版本、目标 ABI、命令和必需路径
|-- SHA256SUMS      # bundle 内所有普通文件的 SHA-256
|-- licenses/       # 已收集的许可证和说明
`-- rootfs/         # 合并到目标系统根目录的文件
```

## 完整性校验

在仓库根目录执行：

```sh
for bundle in bundles/*; do
    (cd "$bundle" && sha256sum -c SHA256SUMS) || exit 1
done
```

## 部署

将需要的 bundle 以保留权限和符号链接的方式合并到目标根文件系统。例如在主机上
整理离线 rootfs：

```sh
sudo cp -a bundles/native-gcc/rootfs/. /path/to/target-rootfs/
```

多个 bundle 可以依次合并。部署前应阅读对应的 `manifest.yaml`，并确认其中的
`required_paths` 在目标系统中存在且类型正确。

原生 GCC 的固定路径为：

```text
/opt/native-toolchain
/opt/native-sysroot
```

在 LA32R 目标系统上可进行基本检查：

```sh
export PATH=/opt/native-toolchain/bin:$PATH
gcc --version
gcc -dumpmachine
gcc -print-sysroot
```

预期目标三元组为 `loongarch32r-linux-gnusf`，sysroot 为
`/opt/native-sysroot`。

## 维护约定

- 更新 bundle 后必须同步更新 `manifest.yaml` 和 `SHA256SUMS`。
- 不要在不支持 LA32R `ilp32s` 的主机上直接执行其中的 ELF 文件。
- `rootfs` 中的可执行权限和符号链接属于运行时布局的一部分，打包和部署时必须保留。
- 构建验证、QEMU 验证和开发板验证是不同证据层级；未完成开发板验证时不得标记为板端已验证。

## 许可证与发布边界

各 bundle 的许可证材料位于其 `licenses/` 目录。部分预构建组件来自历史运行时，
现有 NOTICE 明确指出第三方许可证、对应源码或再分发材料尚未完成全面审计。因此本仓库
当前应作为 CPUSTC 内部工程制品使用，不应仅凭现有元数据制作公开发行版。任何公开发布
前都需要完成逐组件许可证、对应源码和再分发义务审计。
