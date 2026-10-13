# 项目路线图

最后更新：2026-10-13

## 当前目标

- 仅支持 x86_64 设备。
- 默认构建 ImmortalWrt 25.12.2，保留 24.10 和其他 25.12 补丁版本作为回退选项。
- 默认使用 PPPoE，并通过 GitHub Actions Secrets 注入账号和密码。
- 最终固件固定包含 OpenClash、Passwall、TurboACC 和 sirpdboy 设置向导。

## 已完成

- [x] 删除非 x86_64 平台及其构建工作流。
- [x] 合并 x86_64 IMG/ISO 构建流程。
- [x] 保留版本、RootFS 大小、Docker、iStore、PPPoE 和 WAN 管理访问选项。
- [x] 将 PPPoE 凭据迁移到 GitHub Actions Secrets，避免提交明文账号密码。
- [x] 将默认版本设为 ImmortalWrt 25.12.2。
- [x] 将项目迁移到独立仓库 `AXY520/ImmortalWrt-x86-64-Builder`。

## 下一步

按顺序执行：

- [ ] 在正确的路由器上备份现有 OpenClash 配置；备份保存在仓库外，不提交订阅、节点或认证信息。
- [ ] 核对 ImmortalWrt 25.12.2 x86_64 下的 APK 包名、来源及依赖：
  - OpenClash
  - Passwall
  - TurboACC
  - sirpdboy 设置向导
- [ ] 将上述四项固定加入 25.12 构建，并继续保留可编辑的软件包清单。
- [ ] 在新仓库配置 `PPPOE_ACCOUNT` 和 `PPPOE_PASSWORD` Repository Secrets。
- [ ] 运行 Shell 语法、YAML、`actionlint` 和软件包解析检查。
- [ ] 完整构建一次 25.12.2 x86_64 EFI IMG，并确认四个插件均已进入固件。
- [ ] 使用不保留旧系统配置的方式升级；联网正常后再按需恢复 OpenClash 配置。
- [ ] 验证 PPPoE、LAN 地址、DNS、防火墙、Docker 和 LuCI 插件页面。

## 完成标准

- 25.12.2 构建成功并发布 EFI IMG；可选 ISO 构建仍可用。
- 首次启动后 PPPoE 自动拨号，WAN 管理访问保持关闭。
- OpenClash、Passwall、TurboACC 和 sirpdboy 设置向导可在 LuCI 中打开并正常保存配置。
- OpenClash 配置能够从独立备份中恢复，任何敏感备份均未进入 Git 历史。
