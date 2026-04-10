# Docker 安装与使用指南（新手入门）
# Docker Installation & Usage Guide (Beginner)

---

## 目录 / Table of Contents

1. [安装 Docker / Install Docker](#1-安装-docker--install-docker)
2. [验证安装 / Verify Installation](#2-验证安装--verify-installation)
3. [运行 GPT4Free / Run GPT4Free](#3-运行-gpt4free--run-gpt4free)
4. [常见问题 / Troubleshooting](#4-常见问题--troubleshooting)

---

## 1. 安装 Docker / Install Docker

### Windows

1. 访问 Docker 官网下载安装包：  
   Go to the Docker website and download the installer:  
   **https://docs.docker.com/desktop/install/windows-install/**

2. 下载 **Docker Desktop for Windows**，双击安装包运行。  
   Download **Docker Desktop for Windows** and run the installer.

3. 安装过程中保持默认选项，完成后**重启电脑**。  
   Keep the default options during installation and **restart your computer** when prompted.

4. 重启后启动 **Docker Desktop**，等待 Docker 图标在任务栏变为绿色（表示运行中）。  
   After restarting, launch **Docker Desktop** and wait until the Docker icon in the taskbar turns green (running).

> **提示 / Note:** Windows 需要开启 WSL 2 或 Hyper-V。安装程序通常会自动处理，如遇问题请参考官方文档。  
> Windows requires WSL 2 or Hyper-V. The installer usually handles this automatically. If you encounter issues, refer to the official docs.

---

### macOS

1. 访问以下链接下载：  
   Download from:  
   **https://docs.docker.com/desktop/install/mac-install/**

2. 根据芯片类型选择对应版本：  
   Choose the version for your chip:
   - **Apple Silicon (M1/M2/M3)** → 下载 Apple Silicon 版本
   - **Intel** → 下载 Intel 版本

3. 打开下载的 `.dmg` 文件，将 Docker 拖到 Applications 文件夹。  
   Open the `.dmg` file and drag Docker to the Applications folder.

4. 从 Applications 启动 Docker Desktop，等待菜单栏中 Docker 图标稳定。  
   Launch Docker Desktop from Applications and wait for the menu bar icon to become steady.

---

### Linux (Ubuntu / Debian)

在终端中依次执行以下命令：  
Run the following commands in your terminal one by one:

```bash
# 1. 更新软件包列表 / Update package list
sudo apt-get update

# 2. 安装依赖 / Install dependencies
sudo apt-get install -y ca-certificates curl

# 3. 添加 Docker 官方 GPG 密钥 / Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 4. 添加 Docker 软件源 / Add Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. 再次更新并安装 Docker / Update again and install Docker
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# 6. 将当前用户加入 docker 组，避免每次使用 sudo / Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

> **提示 / Note:** 其他 Linux 发行版请参考：https://docs.docker.com/engine/install/

---

## 2. 验证安装 / Verify Installation

安装完成后，打开终端（Windows 用 PowerShell 或命令提示符），运行：  
After installation, open a terminal (Windows: PowerShell or Command Prompt) and run:

```bash
docker --version
```

看到类似 `Docker version 27.x.x` 的输出说明安装成功。  
If you see output like `Docker version 27.x.x`, the installation was successful.

同时检查 Docker Compose：  
Also check Docker Compose:

```bash
docker compose version
```

---

## 3. 运行 GPT4Free / Run GPT4Free

### 快速启动（推荐新手）/ Quick Start (Recommended for Beginners)

#### 方法一：使用 `docker compose`（推荐）/ Method 1: Using `docker compose` (Recommended)

1. 克隆或下载本项目：  
   Clone or download this project:
   ```bash
   git clone https://github.com/xtekky/gpt4free.git
   cd gpt4free
   ```

2. 启动服务：  
   Start the service:
   ```bash
   docker compose up
   ```

3. 浏览器访问：`http://localhost:8080/chat/`  
   Open in your browser: `http://localhost:8080/chat/`

4. 停止服务：  
   Stop the service:
   ```bash
   docker compose down
   ```

---

#### 方法二：直接运行 Docker 镜像 / Method 2: Run Docker Image Directly

**完整版（支持浏览器自动化）/ Full image (supports browser automation):**

```bash
# Linux / macOS
# 完整版容器内进程以 UID 1200 / GID 1201 运行，需提前设置目录权限
# The full image process runs as UID 1200 / GID 1201 inside the container
mkdir -p ${PWD}/har_and_cookies ${PWD}/generated_media
sudo chown -R 1200:1201 ${PWD}/har_and_cookies ${PWD}/generated_media

docker run -p 8080:8080 -p 7900:7900 \
  --shm-size="2g" \
  -v ${PWD}/har_and_cookies:/app/har_and_cookies \
  -v ${PWD}/generated_media:/app/generated_media \
  hlohaus789/g4f:latest
```

```powershell
# Windows PowerShell
mkdir har_and_cookies, generated_media -ErrorAction SilentlyContinue

docker run -p 8080:8080 -p 7900:7900 `
  --shm-size="2g" `
  -v "${PWD}/har_and_cookies:/app/har_and_cookies" `
  -v "${PWD}/generated_media:/app/generated_media" `
  hlohaus789/g4f:latest
```

**轻量版（适合低配置设备）/ Slim image (for lower-spec devices):**

```bash
# Linux / macOS
# 轻量版容器内进程以 UID 1000 / GID 1000 运行（与完整版不同）
# The slim image process runs as UID 1000 / GID 1000 (different from the full image)
mkdir -p ${PWD}/har_and_cookies ${PWD}/generated_media
sudo chown -R 1000:1000 ${PWD}/har_and_cookies ${PWD}/generated_media

docker run -p 8080:8080 -p 1337:8080 \
  -v ${PWD}/har_and_cookies:/app/har_and_cookies \
  -v ${PWD}/generated_media:/app/generated_media \
  hlohaus789/g4f:latest-slim
```

启动后在浏览器中访问：**http://localhost:8080/chat/**  
After starting, visit in your browser: **http://localhost:8080/chat/**

---

## 安装顺序总结 / Installation Order Summary

```
1. 安装 Docker Desktop / Install Docker Desktop
       ↓
2. 重启电脑（如提示）/ Restart computer (if prompted)
       ↓
3. 启动 Docker Desktop，等待其运行 / Start Docker Desktop, wait for it to run
       ↓
4. 打开终端，验证：docker --version / Open terminal, verify: docker --version
       ↓
5. 克隆项目（可选）/ Clone project (optional)
       ↓
6. 运行 docker compose up 或 docker run 命令
       ↓
7. 浏览器访问 http://localhost:8080/chat/
```

---

## 4. 常见问题 / Troubleshooting

### ❓ 提示"Cannot connect to the Docker daemon" / "Cannot connect to the Docker daemon"

- **原因 / Cause:** Docker Desktop 未启动  
- **解决 / Fix:** 打开 Docker Desktop 应用，等待图标变绿后再执行命令。  
  Open Docker Desktop and wait for the icon to turn green before running commands.

---

### ❓ Windows 提示需要启用 WSL 2 / Windows asks to enable WSL 2

- 在 PowerShell（管理员）中运行：  
  Run in PowerShell (as Administrator):
  ```powershell
  wsl --install
  ```
- 重启电脑后重试。/ Restart your computer and try again.

---

### ❓ 端口被占用 / Port already in use

- 将命令中的 `-p 8080:8080` 改为 `-p 9090:8080`（或其他未占用端口），  
  然后访问 `http://localhost:9090/chat/`  
  Change `-p 8080:8080` to `-p 9090:8080` (or another free port) in the command,  
  then visit `http://localhost:9090/chat/`

---

### ❓ 下载镜像很慢 / Image download is slow

- 国内用户可配置 Docker 镜像加速器。在 Docker Desktop → Settings → Docker Engine 中添加：  
  Chinese users can configure a Docker mirror. In Docker Desktop → Settings → Docker Engine, add:
  ```json
  {
    "registry-mirrors": [
      "https://mirror.ccs.tencentyun.com",
      "https://hub-mirror.c.163.com"
    ]
  }
  ```
- 点击 **Apply & Restart**。

---

### ❓ 需要更多帮助 / Need more help?

- 官方文档 / Official docs: https://docs.docker.com/get-started/
- 项目问题 / Project issues: https://github.com/xtekky/gpt4free/issues
- 社区 / Community: Telegram https://telegram.me/g4f_channel
