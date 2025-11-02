# Docker Compose 详细教程

## 目录
1. [Docker Compose 简介](#docker-compose-简介)
2. [Docker Compose 安装](#docker-compose-安装)
3. [Docker Compose 指令和参数说明](#docker-compose-指令和参数说明)
4. [Docker Compose 复杂案例](#docker-compose-复杂案例)
   - [案例 1: 多容器 Web 应用](#案例-1-多容器-web-应用)
   - [案例 2: 微服务架构](#案例-2-微服务架构)
   - [案例 3: 数据库集群](#案例-3-数据库集群)
   - [案例 4: 负载均衡与反向代理](#案例-4-负载均衡与反向代理)
   - [案例 5: 持续集成与部署](#案例-5-持续集成与部署)
   - [案例 6: 多环境配置](#案例-6-多环境配置)

## Docker Compose 简介
Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过一个 YAML 文件来配置应用的服务，然后使用一个命令即可启动和停止所有服务。

## Docker Compose 安装
在安装 Docker Compose 之前，请确保已经安装了 Docker。

### 在 Linux 上安装 Docker Compose
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 在 Windows 和 macOS 上安装 Docker Compose
Docker Desktop 已经包含了 Docker Compose，因此无需单独安装。

## Docker Compose 指令和参数说明

### 常用指令
- `docker-compose up`: 启动服务
- `docker-compose down`: 停止并移除容器
- `docker-compose build`: 构建或重新构建服务
- `docker-compose logs`: 查看服务日志
- `docker-compose ps`: 列出所有容器
- `docker-compose exec`: 在运行的容器中执行命令

### 常用参数
- `-d`: 后台运行容器
- `--build`: 在启动容器前构建镜像
- `--scale`: 扩展服务的实例数量
- `--no-deps`: 不启动依赖的服务

## Docker Compose 复杂案例

### 案例 1: 多容器 Web 应用

#### 案例介绍
本案例展示如何使用 Docker Compose 启动一个包含 Web 服务器和数据库的多容器应用。

#### 代码
```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
    depends_on:
      - db
  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
```

#### 代码注释
- `version: '3'`: 指定 Docker Compose 文件版本。
- `services`: 定义服务。
- `web`: Web 服务，使用 Nginx 镜像。
- `ports`: 将主机的 80 端口映射到容器的 80 端口。
- `depends_on`: 指定依赖的服务，确保 `db` 服务在 `web` 服务之前启动。
- `db`: 数据库服务，使用 MySQL 镜像。
- `environment`: 设置环境变量，这里是 MySQL 的 root 用户密码。

#### 代码解释和说明
- 该配置定义了两个服务：`web` 和 `db`。
- `web` 服务使用 Nginx 镜像，并将主机的 80 端口映射到容器的 80 端口。
- `db` 服务使用 MySQL 镜像，并设置了 root 用户的密码。
- `depends_on` 确保 `db` 服务在 `web` 服务之前启动。

### 案例 2: 微服务架构

#### 案例介绍
本案例展示如何使用 Docker Compose 启动一个包含多个微服务的应用。

#### 代码
```yaml
version: '3'
services:
  user-service:
    image: user-service
    ports:
      - "8081:8080"
  order-service:
    image: order-service
    ports:
      - "8082:8080"
  product-service:
    image: product-service
    ports:
      - "8083:8080"
```

#### 代码注释
- `user-service`: 用户服务，使用自定义镜像 `user-service`。
- `order-service`: 订单服务，使用自定义镜像 `order-service`。
- `product-service`: 产品服务，使用自定义镜像 `product-service`。
- `ports`: 将主机的端口映射到容器的端口。

#### 代码解释和说明
- 该配置定义了三个微服务：`user-service`、`order-service` 和 `product-service`。
- 每个服务使用自定义镜像，并将主机的不同端口映射到容器的 8080 端口。

### 案例 3: 数据库集群

#### 案例介绍
本案例展示如何使用 Docker Compose 启动一个包含主从复制的 MySQL 数据库集群。

#### 代码
```yaml
version: '3'
services:
  master:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: masterpass
    ports:
      - "3306:3306"
  slave:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: slavepass
      MYSQL_MASTER_HOST: master
      MYSQL_MASTER_USER: root
      MYSQL_MASTER_PASSWORD: masterpass
    depends_on:
      - master
```

#### 代码注释
- `master`: 主数据库服务，使用 MySQL 镜像。
- `slave`: 从数据库服务，使用 MySQL 镜像。
- `environment`: 设置环境变量，包括主从复制的配置。
- `depends_on`: 确保 `master` 服务在 `slave` 服务之前启动。

#### 代码解释和说明
- 该配置定义了一个主从复制的 MySQL 数据库集群。
- `master` 服务是主数据库，`slave` 服务是从数据库。
- `slave` 服务通过环境变量配置主从复制，确保数据同步。

### 案例 4: 负载均衡与反向代理

#### 案例介绍
本案例展示如何使用 Docker Compose 启动一个包含负载均衡和反向代理的 Web 应用。

#### 代码
```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
    depends_on:
      - app1
      - app2
  app1:
    image: myapp
    environment:
      APP_NAME: app1
  app2:
    image: myapp
    environment:
      APP_NAME: app2
  lb:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - web
```

#### 代码注释
- `web`: Web 服务，使用 Nginx 镜像。
- `app1` 和 `app2`: 应用服务，使用自定义镜像 `myapp`。
- `lb`: 负载均衡服务，使用 Nginx 镜像。
- `volumes`: 挂载 Nginx 配置文件。

#### 代码解释和说明
- 该配置定义了一个包含负载均衡和反向代理的 Web 应用。
- `web` 服务是前端 Web 服务器，`app1` 和 `app2` 是后端应用服务。
- `lb` 服务是负载均衡器，通过挂载的 Nginx 配置文件实现负载均衡。

### 案例 5: 持续集成与部署

#### 案例介绍
本案例展示如何使用 Docker Compose 实现持续集成与部署。

#### 代码
```yaml
version: '3'
services:
  jenkins:
    image: jenkins/jenkins:lts
    ports:
      - "8080:8080"
    volumes:
      - jenkins_home:/var/jenkins_home
  gitlab:
    image: gitlab/gitlab-ce:latest
    ports:
      - "80:80"
    volumes:
      - gitlab_config:/etc/gitlab
      - gitlab_logs:/var/log/gitlab
      - gitlab_data:/var/opt/gitlab
volumes:
  jenkins_home:
  gitlab_config:
  gitlab_logs:
  gitlab_data:
```

#### 代码注释
- `jenkins`: Jenkins 服务，用于持续集成。
- `gitlab`: GitLab 服务，用于代码托管和持续部署。
- `volumes`: 挂载数据卷，确保数据持久化。

#### 代码解释和说明
- 该配置定义了一个包含 Jenkins 和 GitLab 的持续集成与部署环境。
- `jenkins` 服务用于构建和测试代码，`gitlab` 服务用于代码托管和部署。
- 通过挂载数据卷，确保 Jenkins 和 GitLab 的数据持久化。

### 案例 6: 多环境配置

#### 案例介绍
本案例展示如何使用 Docker Compose 实现多环境配置。

#### 代码
```yaml
version: '3'
services:
  app:
    image: myapp
    environment:
      - ENV=development
    ports:
      - "8080:8080"
  app-prod:
    image: myapp
    environment:
      - ENV=production
    ports:
      - "8081:8080"
```

#### 代码注释
- `app`: 开发环境应用服务。
- `app-prod`: 生产环境应用服务。
- `environment`: 设置环境变量，区分开发和生产环境。

#### 代码解释和说明
- 该配置定义了两个环境的应用服务：`app` 和 `app-prod`。
- 通过环境变量 `ENV` 区分开发和生产环境。
- 开发环境使用 8080 端口，生产环境使用 8081 端口。

---

通过以上案例，您可以深入了解 Docker Compose 的使用方法，并应用于实际项目中。