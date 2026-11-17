# Kasama · 官方发行版

> 本仓库为**公开发行仓**，只存放发行物：Windows / Linux 部署包、KasamaStrike 运行时、
> 签名清单（manifest）以及部署与使用文档。**源码与内部开发文档不在本仓库**。

- 下载：见 [Releases](../../releases)
- 安装：[`docs/DEPLOYMENT.md`](docs/DEPLOYMENT.md)
- 使用：[`docs/USAGE.md`](docs/USAGE.md)
- 升级：[`docs/UPDATE.md`](docs/UPDATE.md)
- 安全：[`SECURITY.md`](SECURITY.md)

**当前版本：6.4.0**（2026-11-17）· 下载见 [Releases](../../releases/tag/v6.4.0)

## 6.4 更新摘要

- 执行边界：scope 抽取不再把 `php://`、说明文本、本机回环地址判成越界；
  任务范围支持 `*.example.com` 通配符，ctf2 工具不再被范围闸误拒。
- 稳定性：同 lane 多会话不再互相覆盖守卫配置导致暂停后无法恢复；
  主 Agent 收尾后自动唤醒规划器。
- 规划器唤醒预算默认关闭，消除空转唤醒干扰。
- 前端治理：预算硬暂停、意图时间盒、看门狗、策略拒绝暂停、漏洞人工审批、
  唤醒预算等强阻断能力全部可开关；按钮点击与设置变更写入系统日志，便于溯源。
- 自进化/反思闭环的 8 项修复一并包含。

## 下载哪个包

| 平台 | 文件 | 说明 |
|---|---|---|
| Windows x64 | `kasama-<版本>-windows-amd64.zip` | 解压后运行 `Install.bat` |
| Linux x64 | `kasama-<版本>-linux-amd64.tar.gz` / `.zip` | 解压后运行 `deploy/linux/install.sh`；`.zip` 供一键更新器取包 |
| 运行时 | `kasamastrike-<os>-<arch>[.exe]` | 智能体运行时（平台会自动调用） |
| 校验 | `SHA256SUMS-<版本>.txt` / `manifest.json` + `manifest.json.sig` | 见下 |

`-plain` 结尾的是未混淆排障版，功能一致、保留符号与行号；对外交付建议用混淆版。

## 三步启动（Windows）

```powershell
# 1) 解压后进入目录
# 2) 一键安装（自动拉起 PostgreSQL 容器、生成随机密钥、启动平台）
.\Install.bat
# 3) 打开 http://127.0.0.1:8790/ ，设置管理员密码
```

Linux：

```bash
tar -xzf kasama-<版本>-linux-amd64.tar.gz
cd kasama-<版本>-linux-amd64
bash deploy/linux/install.sh
```

默认端口：平台 `8790`、MCP 网关 `8791`、录制代理 `8799`、出口网关 `8798`、运行时 `4098`、
PostgreSQL `5433`（仅监听 127.0.0.1）。

## 一键更新

本仓库公开，平台默认匿名访问 GitHub API 与发布资产，无需凭据。
若改用私有镜像或受限访问，可选用只读凭据：

```bash
KASAMA_UPDATE_GITHUB_TOKEN=<GitHub 只读 PAT>
```

- 只放在平台进程环境或 `deploy/<os>/.env`，不要写入 `config.json`、前端或任何会提交的文件。
- 该凭据只用于 `api.github.com` 与发布资产下载，不下发前端、不写日志。

## 完整性校验

```bash
sha256sum -c SHA256SUMS-<版本>.txt
```

`manifest.json` 由发布私钥签名（Ed25519），平台内置公钥验签；手工校验见 `docs/UPDATE.md`。

## 系统要求

| 项 | 要求 |
|---|---|
| OS | Windows 10/11 x64、主流 Linux x64（glibc 2.28+） |
| CPU/内存 | 4 核 / 8GB 起（10 并发以上建议 16GB） |
| 依赖 | Docker（推荐，用于 PostgreSQL 16）或已有 PostgreSQL 13+ |
| 网络 | 平台自身只需访问你配置的 LLM 接口；被评估目标需可达 |

## 支持

- 使用问题：本仓库 Issues
- 安全问题：请勿公开提交，按 [`SECURITY.md`](SECURITY.md) 私下报告

## 许可

二进制与随附文档按 **AGPL-3.0** 分发（见 [`LICENSE`](LICENSE)）。
本仓库不提供源代码；源码与商业授权请联系维护者。
