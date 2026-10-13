# x86-64 ImmortalWrt Builder

这是一个仅面向 x86_64 设备的 ImmortalWrt ImageBuilder 自动化项目。仓库保留 24.10 和 25.12 两条版本线，并通过一条 GitHub Actions 工作流生成 EFI 固件；需要时也可继续封装为 ISO 安装器。

本项目是第三方构建脚本，与 ImmortalWrt 官方无关。遇到自定义固件问题时，请先检查本仓库的工作流、软件包清单和首次启动配置。

## 构建产物

- `img`：生成 `squashfs-combined-efi.img.gz`，适合直接写入磁盘。
- `img+iso`：除 EFI IMG 外，再生成带 `ddd` 安装命令的 ISO 安装器。
- 所有产物由 GitHub Actions 上传到 Releases。

## 可选参数

| 参数 | 默认值 | 说明 |
| --- | --- | --- |
| ImmortalWrt 版本 | `25.12.2` | 可选择 24.10.0–24.10.6 或 25.12.0–25.12.2 |
| 构建产物 | `img` | 可选择仅 IMG 或 IMG + ISO |
| RootFS 大小 | `1G` | 可选择 1G、2G、3G、4G |
| LAN 管理地址 | `192.168.100.1` | 只用于多网口模式 |
| Docker | `yes` | 可关闭 |
| iStore | `false` | 可开启 |
| PPPoE | `yes` | 默认开启 |
| WAN 管理访问 | `no` | 默认拒绝从 WAN 访问管理服务 |

## 配置 PPPoE 凭据

不要把宽带账号和密码写入工作流、提交记录或手动输入参数。请在 GitHub 仓库中打开：

`Settings → Secrets and variables → Actions → New repository secret`

添加两个 Repository secrets：

- `PPPOE_ACCOUNT`：宽带账号。
- `PPPOE_PASSWORD`：宽带密码。

启用 PPPoE 时，如果任一 Secret 缺失，构建会立即失败。凭据不会写入 Actions 日志；固件首次启动并写入 UCI 后，也会删除临时凭据文件。

## 手动构建

1. 打开仓库的 `Actions` 页面。
2. 选择 `Build x86-64 ImmortalWrt`。
3. 点击 `Run workflow`。
4. 选择版本、产物类型和其他参数。
5. 构建完成后，从对应 Release 下载固件。

## 自动构建

向 `master` 分支推送以下路径的修改时，会自动构建：

- `.github/workflows/build-x86-64.yml`
- `files/**`
- `shell/**`
- `x86-64/**`

自动构建使用默认参数：25.12.2、1G RootFS、EFI IMG、Docker 开启、iStore 关闭、PPPoE 开启、WAN 管理访问关闭。

## 修改软件包

24.10 和 25.12 使用不同的软件包格式，因此保留两套可编辑清单：

- 24.10：编辑 `shell/custom-packages.sh`，使用 IPK/OPKG 软件包。
- 25.12：编辑 `shell/apk-custom-packages.sh`，使用 APK 软件包。

两个文件都保留了上游项目的注释和可选包。取消某一行开头的 `#` 即可启用对应软件包；在包名前添加 `-` 可以排除 ImageBuilder 的默认包。

固件固定加入的基础软件位于：

- `x86-64/build24.sh`
- `x86-64/build25.sh`

修改软件包清单并推送到 `master` 后，会自动触发一次默认构建。

## 网络规则

- 多网口设备使用第一个检测到的 `eth*` 或 `en*` 接口作为 WAN，其余接口加入 LAN。
- WAN 默认使用 PPPoE；取消 PPPoE 后改用 DHCP。
- LAN 默认地址为 `192.168.100.1/24`，可在手动构建时修改。
- 单网口设备保持 DHCP 客户端模式。
- WAN 管理访问默认关闭。如确有需要，可在手动构建时显式开启。
- 默认用户名为 `root`，初始密码为空。首次登录后应立即设置密码。

## 保留的目录

```text
.github/workflows/   x86_64 构建与运行记录清理
files/               固件文件覆盖层和首次启动配置
shell/               24.10/25.12 软件包清单与解包脚本
x86-64/              ImageBuilder 配置和构建脚本
```

## 上游项目

本项目基于 ImmortalWrt ImageBuilder 和原 AutoBuildImmortalWrt 项目整理。原项目及相关第三方软件仍归各自作者所有，许可证见 [LICENSE](LICENSE)。
