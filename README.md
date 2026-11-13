# Kasama · 官方发行版

> 本仓库**只存放发行物**：Windows / Linux 部署包、KasamaStrike 运行时、签名清单（manifest）
> 以及部署与使用文档。**源码不在本仓库**。

- 下载：见 [Releases](../../releases)
- 安装：[`docs/DEPLOYMENT.md`](docs/DEPLOYMENT.md)
- 使用：[`docs/USAGE.md`](docs/USAGE.md)
- 升级：[`docs/UPDATE.md`](docs/UPDATE.md)
- 安全：[`SECURITY.md`](SECURITY.md)

**当前版本：6.1.0**（2026-11-13）· 下载见 [Releases](../../releases/tag/v6.1.0)

## 下载哪个包

| 平台 | 文件 | 说明 |
|---|---|---|
| Windows x64 | `kasama-<版本>-windows-amd64.zip` | 解压后运行 `Install.bat` |
| Linux x64 | `kasama-<版本>-linux-amd64.tar.gz` | 解压后运行 `deploy/linux/install.sh` |
| 运行时 | `kasamastrike-<os>-<arch>[.exe]` | 智能体运行时（平台会自动调用） |
| 校验 | `SHA256SUMS-<版本>.txt` / `manifest.json` + `manifest.json.sig` | 见下 |

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
