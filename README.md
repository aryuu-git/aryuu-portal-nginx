# aryuu 服务器门户（aryuu-portal-nginx）

`http://101.34.245.28/` 的入口页：纯静态、无后端、无构建。
卡片链接已配置完成（karuta → `/login`，anitexas → `/anitexas/`），**上传即用，无需编辑**。

## 目录

- `portal.html` — 成品门户页（上传到 `/opt/aryuu-portal/portal.html`，原样部署）
- `nginx.conf` — 门户相关的 location 片段（存档参考）
- `backups/` — 线上 nginx 配置快照（基线对照，勿直接部署）；部署件见仓库根 `nginx-single80-subpath.conf`

## 纯 IP 单端口（80）三站路径分配

| 路径 | 服务 | 说明 |
|---|---|---|
| `= /`（裸根） | **门户（本目录）** | `portal.html`；文件名刻意避开 karuta SPA 回退用的 `/index.html` |
| `/anitexas/` | anitexas | 子路径化前端（vite base=/anitexas/），API/WS 反代 8081 |
| 其余全部 | karuta | 前端 dist + `/api/` `/ws/` `/uploads/` 反代 8080 |

完整 server 块（**唯一部署件**）：`nginx-single80-subpath.conf`（仓库根）。

## 部署

```bash
mkdir -p /opt/aryuu-portal
# 上传 portal.html → /opt/aryuu-portal/portal.html（原样，无任何修改）
```

nginx 按 `nginx-single80-subpath.conf` 替换整个 server 块后：
`nginx -t && systemctl reload nginx`。

## 验收

浏览器开 `http://101.34.245.28/`：

1. 根路径出门户（aryuu.icu 双卡）
2. 点 AniTexas 卡 → `/anitexas/` 大厅，建房进对局能发牌（= WS 通）
3. 点 Karuta 卡 → `/login`

已知折中：karuta 首页占的是精确 `/` 之外的路径，站内跳转正常；在 karuta 首页按 F5 会回到门户，点卡片再进即可。

## 改版

替换 `/opt/aryuu-portal/portal.html` 即可，nginx 无需 reload（入口 no-cache）。
