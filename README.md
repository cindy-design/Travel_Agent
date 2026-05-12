

# Travel_Agent - 智能旅游攻略生成器

一个基于AI的智能旅游攻略生成系统，提供个性化的旅行方案规划。

## 功能特性

- **智能Agent**: 基于大语言模型的旅游规划助手
- **多源数据**: 整合航班、酒店、景点、天气等多维度信息
- **智能爬虫**: 自动补充缺失数据，确保信息完整性
- **数据清洗**: 智能甄别和可信度评分系统
- **可视化展示**: 地图集成和方案对比视图
- **响应式设计**: 支持多设备访问
- **实时更新**: 后台任务持续更新数据

## 技术架构

### 后端
- **FastAPI**: 高性能API框架
- **SQLAlchemy**: ORM数据库操作
- **Celery**: 异步任务处理
- **Redis**: 缓存和消息队列
- **MCP Tools**: 模型控制协议工具集成

### 前端
- **React**: 现代化前端框架
- **TypeScript**: 类型安全
- **Tailwind CSS**: 样式框架
- **Leaflet**: 地图组件
- **Ant Design**: UI组件库

### AI & 数据
- **OpenAI API**: 大语言模型
- **Scrapy**: 网页爬虫
- **Pandas**: 数据处理
- **NumPy**: 数值计算

## 项目结构

```
LX_SkyRoam_Agent/
├── backend/                 # 后端服务
│   ├── app/
│   │   ├── api/            # API路由
│   │   ├── core/           # 核心配置
│   │   ├── mcp/            # MCP工具
│   │   ├── models/         # 数据模型
│   │   ├── platforms/      # 平台相关(小红书爬虫)
│   │   ├── services/       # 业务逻辑
│   │   ├── tasks/          # celery异步任务
│   │   ├── tools/          # 工具
│   ├── scripts/            # 脚本
│   ├── tests/              # 测试
│   ├── uploads/            # 上传文件目录（挂载静态文件）
│   ├── logs/               # 日志目录
│   ├── .gitignore
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── .env.example        # 环境变量示例
│   ├── .env                # 环境变量
│   ├── requirements.txt    # 依赖包
│   └── main.py             # 应用入口
├── frontend/               # 前端应用
│   ├── src/
│   │   ├── components/     # React组件
│   │   ├── pages/          # 页面
│   │   ├── services/       # API服务
│   │   └── utils/          # 工具函数
│   ├── package.json
│   └── public/
├── database/              # 数据库相关
│   ├── migrations/        # 数据库迁移
│   └── init.sql
├── docker/               # Docker配置
├── docs/                 # 文档
└── tests/                # 测试文件
```

## 快速开始

### 环境要求
- [Python 3.10+](https://www.python.org/downloads/)
- [Node.js 18+](https://nodejs.org/en/download)
- [Redis](https://redis.io/download)
- [PostgreSQL](https://www.postgresql.org/download/)

### 安装步骤

1. 克隆项目
```bash
git clone <repository-url>
cd LX_SkyRoam_Agent
```

2. 后端设置
```bash
cd backend
pip install -r requirements.txt
```

3. 前端设置
```bash
cd frontend
npm install
```

4. 环境配置
```bash
cp .env.example .env
# 编辑 .env 文件，配置数据库和API密钥
```

5. 启动服务
```bash
# 启动后端
cd backend && uvicorn main:app

# 启动Celery Worker
cd backend && celery -A app.core.celery worker --loglevel=info

# 启动前端
cd frontend && npm start

# 启动高德API服务
cd backend && python mcp_http_server_amap.py

# 启动小红书爬虫服务
cd backend && python xhs_api_server.py

```

### Windows 一键启动

- 运行根目录中的 `start-all-win.bat`，脚本会自动激活 `skyroam` Conda 环境，并在独立窗口中依次启动 FastAPI、Celery、前端、高德 API 以及小红书服务。

## 使用说明

1. 在前端输入旅行需求（目的地、天数、偏好、预算）
2. AI Agent分析需求并收集相关信息
3. 系统生成多个旅行方案供选择
4. 用户可以调整和细化方案
5. 导出最终方案（PDF、图片、分享链接）


