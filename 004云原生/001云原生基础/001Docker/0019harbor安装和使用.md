
# Harbor 简介、安装与使用指南

## 1. 简介 Harbor

Harbor 是一个开源的企业级 Docker Registry 管理工具，由 VMware 公司开源并维护。它提供了镜像的存储、分发、安全扫描、用户管理等功能，适用于企业级容器镜像仓库的管理需求。Harbor 支持多租户、镜像复制、漏洞扫描等高级功能，是 Kubernetes 和 Docker 生态中的重要组件。

---

## 2. Harbor 的版本

Harbor 的版本分为社区版和企业版：
- **社区版**：开源免费，功能丰富，适合中小型企业或个人使用。
- **企业版**：提供额外的企业级功能，如高级安全扫描、技术支持等，适合大型企业。

当前最新版本：**v2.7.0**（截至 2023 年 10 月）。

---

## 3. Harbor 的优缺点

### 优点：
- **开源免费**：社区版功能强大且免费。
- **安全性高**：支持镜像漏洞扫描、内容信任、RBAC 权限控制等。
- **易用性**：提供 Web UI 界面，操作简单。
- **多租户支持**：支持多用户、多项目隔离。
- **镜像复制**：支持跨数据中心镜像同步。

### 缺点：
- **资源消耗较高**：对硬件资源要求较高，尤其是存储和内存。
- **部署复杂**：初次部署需要一定的技术基础。
- **依赖较多**：需要依赖 PostgreSQL、Redis 等组件。

---

## 4. Ubuntu 国内安装详细步骤

### 环境准备
- Ubuntu 20.04 LTS
- Docker 20.10+
- Docker Compose 1.29+

### 安装步骤

#### 1. 安装 Docker 和 Docker Compose
```bash
# 更新系统
sudo apt update && sudo apt upgrade -y

# 安装 Docker
sudo apt install -y docker.io

# 启动 Docker
sudo systemctl start docker
sudo systemctl enable docker

# 安装 Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

#### 2. 下载 Harbor 安装包
```bash
# 下载 Harbor 离线安装包
wget https://github.com/goharbor/harbor/releases/download/v2.7.0/harbor-offline-installer-v2.7.0.tgz

# 解压安装包
tar -xzvf harbor-offline-installer-v2.7.0.tgz
cd harbor
```

#### 3. 修改配置文件
编辑 `harbor.yml` 文件，配置域名、端口、数据存储路径等：
```yaml
hostname: harbor.example.com
http:
  port: 80
data_volume: /data/harbor
```

#### 4. 安装 Harbor
```bash
# 执行安装脚本
sudo ./install.sh
```

#### 5. 访问 Harbor
安装完成后，通过浏览器访问 `http://harbor.example.com`，默认用户名：`admin`，密码：`Harbor12345`。

---

## 5. 详细的使用方法

### 1. 登录 Harbor
```bash
docker login harbor.example.com
```

### 2. 推送镜像
```bash
# 打标签
docker tag my-image:latest harbor.example.com/my-project/my-image:latest

# 推送镜像
docker push harbor.example.com/my-project/my-image:latest
```

### 3. 拉取镜像
```bash
docker pull harbor.example.com/my-project/my-image:latest
```

### 4. 管理项目
通过 Web UI 创建项目、设置权限、查看镜像等。

### 5. 镜像复制
在 Web UI 中配置复制规则，实现跨数据中心镜像同步。

---

## 6. 案例 1：企业内部镜像仓库

### 场景描述
某企业需要搭建一个内部镜像仓库，用于存储和管理开发团队的 Docker 镜像。

### 解决方案
1. 部署 Harbor，配置 HTTPS 和 LDAP 认证。
2. 创建多个项目，分别为开发、测试、生产环境。
3. 设置 RBAC 权限，确保不同团队只能访问自己的项目。
4. 定期扫描镜像漏洞，确保安全性。

---

## 7. 案例 2：跨数据中心镜像同步

### 场景描述
某企业在两个数据中心（北京和上海）部署 Kubernetes 集群，需要实现镜像的跨数据中心同步。

### 解决方案
1. 在两个数据中心分别部署 Harbor。
2. 在 Harbor 中配置复制规则，将北京中心的镜像同步到上海中心。
3. 使用 Kubernetes 的镜像拉取策略，优先从本地 Harbor 拉取镜像，减少网络延迟。

---

以上是 Harbor 的简介、安装步骤、使用方法及案例分享。希望对您有所帮助！
```