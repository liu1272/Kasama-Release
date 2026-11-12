# 部署指南

## 1. 组件与端口

| 组件 | 默认监听 | 作用 |
|---|---|---|
| `kasama` | `127.0.0.1:8790` | 控制台（Web UI + API） |
| `kasama-mcp` | `127.0.0.1:8791` | 工具网关（平台 ↔ 运行时） |
| 录制代理 | `127.0.0.1:8799` | HTTP(S) 录制（可选） |
| 出口网关 | `127.0.0.1:8798` | 代理池统一入口（启用后） |
| `kasamastrike` | `127.0.0.1:4098` | 智能体运行时 |
| PostgreSQL | `127.0.0.1:5433` | 唯一数据源（容器） |

## 2. Windows

```powershell
.\Install.bat                 # 安装并启动（需要 Docker Desktop 在运行）
# 只准备、不启动：
.\Install.bat -NoStart
```

安装器会：校验二进制 → 创建/复用 `kasama-pg` 容器 → 创建库与角色 → 生成
`deploy\windows\.env`（随机密钥/口令）与 `config.json` → 端口预检 → 启动三个进程 → 健康检查。

日常运维：

```powershell
deploy\windows\Kasama-Start.bat            # 启动
deploy\windows\Kasama-Stop.bat             # 停止（加 --all 同时停 PostgreSQL）
deploy\windows\Uninstall.bat               # 卸载（可选择保留数据）
```

## 3. Linux

```bash
bash deploy/linux/install.sh
# 停止 / 卸载
bash deploy/linux/stop.sh
bash deploy/linux/uninstall.sh
```

同机已有 PostgreSQL 时可不使用容器：

```bash
export KASAMA_PG_DSN='postgres://用户:口令@127.0.0.1:5432/kasama?sslmode=disable'
bash deploy/linux/install.sh
```

## 4. 反向代理与 HTTPS

平台默认只监听 `127.0.0.1`。对外提供服务时建议放在 Nginx/Caddy 之后：

```nginx
server {
  listen 443 ssl http2;
  server_name kasama.example.com;
  client_max_body_size 200m;      # 流量录制/导出
  location / {
    proxy_pass http://127.0.0.1:8790;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_read_timeout 3600s;     # SSE（任务活动流）
    proxy_buffering off;
  }
}
```

## 5. 首次配置

1. 打开 `http://<主机>:8790/`，设置管理员密码（用户名固定 `Kasama`）；
2. 「系统 → LLM」添加一个 OpenAI 兼容模型（如 DeepSeek：`https://api.deepseek.com/v1`，
   `deepseek-flash`），保存并**设为默认**；
3. 「系统 → 设置」按需调整并发（`workers`）、录制开关（`traffic_capture`）；
4. 新建任务并在「范围」里填授权目标——**未在范围内的目标会被执行侧拦截**。

## 6. 数据与备份

| 内容 | 位置 |
|---|---|
| 数据库 | Docker 卷 `kasama-pgdata`（或你的外部 PG） |
| 任务文件/证据/流量 | `<安装目录>/data/platform/` |
| 密钥 | `bin/jwt.key`、`deploy/windows/.env`（0600，**不可外传**） |

```bash
# 备份
docker exec kasama-pg pg_dump -U kasama -Fc -d kasama -f /tmp/k.dump && docker cp kasama-pg:/tmp/k.dump .
# 恢复（先停服）
docker cp k.dump kasama-pg:/tmp/ && docker exec kasama-pg pg_restore -U kasama -d kasama --no-owner --no-privileges /tmp/k.dump
```

## 7. 升级与卸载

- 升级：见 [`UPDATE.md`](UPDATE.md)（优先在 UI「系统 → 更新」一键执行）。
- 卸载前请确认是否需要保留 `data/` 与数据库卷；`Uninstall.bat` 会询问。

## 8. 常见问题

| 现象 | 处理 |
|---|---|
| 启动报 `KASAMA_CS_URL is required` | 先启动 `kasamastrike`（安装器会自动拉起）；检查 4098 端口 |
| 页面能开但任务不动 | 「系统 → LLM」是否配置并**设为默认**；「系统 → 设置」看 `workers` |
| 任务全部 `blocked` | 展开意图看原因；范围/审批/预算三类最常见 |
| 端口冲突 | 改 `deploy/windows/.env` 与启动参数，或停掉占用进程 |
| 升级校验失败 | 见 `UPDATE.md` 的签名校验小节；不要绕过 |
