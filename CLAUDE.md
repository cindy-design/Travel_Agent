# CLAUDE.md — LX SkyRoam Agent 项目开发指南

## 项目概览
莫柒智能旅游助手（原 LX SkyRoam Agent），基于 FastAPI + React + OpenAI 的智能旅行规划系统。

---

## 环境启动

### Docker Compose（推荐）
```bash
cd D:\LX_SkyRoam_Agent-main
docker compose up -d --force-recreate backend celery-worker amap-mcp-api xhs-api
docker compose up -d --force-recreate frontend
```

### 关键服务端口
| 服务 | 宿主端口 | 容器地址 |
|------|---------|---------|
| 前端 | 13000 | http://localhost:13000 |
| 后端 | 8001 | http://localhost:8001 |
| 高德MCP | 13002 | http://localhost:13002 |
| 小红书API | 18002 | http://localhost:18002 |
| PostgreSQL | (不暴露) | postgres:5432 |
| Redis | (不暴露) | redis:6379 |

---

## 重要⚠️：已解决的问题（不要再犯）

### 1. Docker 构建：apt sources.list 兼容性
- **问题**：Python 3.10-slim (Debian Bookworm) 不再使用 `/etc/apt/sources.list`，改用 `sources.list.d/debian.sources`
- **修复**：backend/Dockerfile 中先检测文件是否存在再 sed
- **正确写法**：
  ```dockerfile
  RUN (if [ -f /etc/apt/sources.list ]; then sed -i ...; fi) && apt-get update ...
  ```

### 2. bcrypt 版本兼容
- **问题**：bcrypt 5.x 移除了 `__about__` 属性，与 passlib 1.7.4 不兼容
- **修复**：requirements.txt 固定 `bcrypt==4.0.1`
- **错误现象**：注册时转圈、日志显示 `AttributeError: module 'bcrypt' has no attribute '__about__'`

### 3. Docker 容器间网络
- **问题**：容器内用 `localhost` 只能访问自己，不能访问其他容器
- **修复**：容器间通信必须用 Docker 服务名
  - `.env.docker` 中 `AMAP_MCP_HTTP_URL=http://amap-mcp-api:3002/mcp`（不是 localhost）
  - 同理 Redis 用 `redis`，PostgreSQL 用 `postgres`

### 4. API Key 绝对不能提交到 Git
- `.env.docker` 已在 `.gitignore` 中排除
- 只提交 `.env.docker.example`（占位符版本）
- `.gitignore` 包含：`.env`、`backend/.env`、`.env.docker`、`.env.local`

### 5. 前端 API URL 配置
- Docker Compose 中前端 `REACT_APP_API_URL` 必须用 `http://localhost:8001`
- 不能用 `http://backend:8001`（浏览器在宿主机，解析不了 Docker 内部域名）
- 同时 docker-compose.yml 中后端必须暴露端口：`ports: - "8001:8001"`

### 6. 限流配置
- 默认 20次/10秒 太严格，地图静态图会被拦截
- .env.docker 中设为：`RATE_LIMIT_MAX_REQUESTS=60`
- 地图接口加入豁免：`RATE_LIMIT_EXCLUDE_PATHS=["/docs","/redoc","/openapi.json","/api/v1/map/static"]`

### 7. 前端修改不生效
- React dev server 的 HMR 在 Docker volume 挂载时可能不触发
- 解决：`docker compose up -d --force-recreate frontend` 强制重建

### 8. CSS 冲突与主题
- 不要用多个文件定义同一类名（App.css vs HomePage.css vs common.css）
- 亮色主题变量在 `common.css` 的 `:root` 中统一定义
- Ant Design v5 组件覆盖必须加 `!important`
- ConfigProvider 的 `components` token 优先级高于 CSS

### 9. Git 推送网络问题
- 国内环境需配置代理：`git config --global http.proxy http://127.0.0.1:端口`
- 本地分支 `master` 推送到远程 `main` 需：`git push origin HEAD:main`

### 10. 端口变量解析
- docker-compose.yml 中 `command` 命令的 `${PORT}` 会被宿主机解析
- 需改为 `$${PORT:-8001}` 让容器内 shell 解析

---

## 前端开发

### 常用命令
```bash
# 强制重建前端
docker compose up -d --force-recreate frontend

# 重启
docker compose restart frontend
```

### 文件结构
- `common.css` — 全局主题变量和 Ant Design 组件覆盖
- `HomePage.css` — 首页专属样式
- `Layout.css` — 导航栏/头部样式
- `App.tsx` — ConfigProvider 主题 token

### 三段式自适应布局
- 大屏 >1200px：1280px 宽 + 32px padding
- 中屏 768-1200px：960px 宽 + 24px padding
- 小屏 <768px：全宽 + 16px padding

---

## 后端开发

### 重启服务
```bash
docker compose restart backend celery-worker amap-mcp-api xhs-api
```

### 调试后端日志
```bash
docker logs skyroam-backend --tail 50
docker logs skyroam-celery-worker --tail 50
```

### API Key 配置位置
- Docker 环境：`.env.docker`（不提交 Git）
- 本地开发：`backend/.env`
- 模板文件：`.env.docker.example` 和 `backend/.env.example`

---

## Git 操作

```bash
# 提交
git add .
git commit -m "feat: 描述"

# 推送（注意分支名差异）
git push origin HEAD:main

# 查看状态
git status
git log --oneline -5
```
