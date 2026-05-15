# syntax=docker/dockerfile:1.7
FROM python:3.10-slim

# 设置工作目录
WORKDIR /app

# 安装系统依赖
RUN --mount=type=cache,target=/var/cache/apt --mount=type=cache,target=/var/lib/apt \
    (if [ -f /etc/apt/sources.list ]; then \
       sed -i 's|deb.debian.org|mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
       sed -i 's|security.debian.org|mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list; \
     elif [ -f /etc/apt/sources.list.d/debian.sources ]; then \
       sed -i 's|http://deb.debian.org|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list.d/debian.sources && \
       sed -i 's|http://security.debian.org|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list.d/debian.sources; \
     fi) && \
    apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        libpq-dev \
        libgl1 \
        libglib2.0-0 \
        libnss3 \
        libnspr4 \
        libatk1.0-0 \
        libatk-bridge2.0-0 \
        libcups2 \
        libdrm2 \
        libdbus-1-3 \
        libxkbcommon0 \
        libxcomposite1 \
        libxdamage1 \
        libxfixes3 \
        libxrandr2 \
        libgbm1 \
        libpango-1.0-0 \
        libcairo2 \
        libasound2 \
        vim \
        curl \
    && rm -rf /var/lib/apt/lists/*

# 复制依赖文件
COPY requirements.txt .

# 安装Python依赖
RUN --mount=type=cache,target=/root/.cache/pip pip install -r requirements.txt -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

# 安装 Playwright 浏览器（Chromium）
RUN playwright install chromium

# 复制应用代码
COPY . .

# 创建必要的目录
RUN mkdir -p logs uploads

# 设置环境变量
ENV PYTHONPATH=/app
ENV PYTHONUNBUFFERED=1
ENV PORT=8001

# 暴露端口
EXPOSE 8001

# 启动命令，支持通过环境变量 PORT 调整端口
CMD ["bash", "-lc", "uvicorn main:app --host 0.0.0.0 --port ${PORT}"]
