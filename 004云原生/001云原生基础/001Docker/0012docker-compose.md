# docker compose用法

`Docker Compose` 是一个用于定义和运行多容器 Docker 应用的工具。通过一个 YAML 文件来配置应用的服务，然后使用一个命令就可以根据配置文件创建并启动所有服务。以下是 `Docker Compose` 的详细用法：

### 1. 安装 Docker Compose

```bash
# ubuntu
apt update
apt -y install docker-compose

# centos
yum -y remove epel-release
rm -rf /etc/yum.repos.d/*
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all && yum makecache
yum -y install epel-release
yum -y install docker-compose

docker-compose --version
```

在不同的操作系统上安装 `Docker Compose` 的方式有所不同，以下以常见的 Linux 系统为例：

```bash
# 下载 Docker Compose 二进制文件
sudo curl -L "https://github.com/docker/compose/releases/download/v2.17.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 添加可执行权限
sudo chmod +x /usr/local/bin/docker-compose

# 验证安装
docker-compose --version
```

### 2. 创建 `docker-compose.yml` 文件

`docker-compose.yml` 是 `Docker Compose` 的核心配置文件，用于定义应用的服务、网络和卷等信息。以下是一个简单的示例：

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    restart: always
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
    volumes:
      - mysql-data:/var/lib/mysql
    restart: always

volumes:
  mysql-data:
```

上述配置文件定义了两个服务：

- `web`：使用 `nginx` 镜像，将容器的 80 端口映射到主机的 80 端口，挂载本地的 `nginx.conf` 文件到容器内，设置容器在退出时自动重启。
- `db`：使用 `mysql:8.0` 镜像，设置 MySQL 的 root 密码和数据库名，挂载一个命名卷 `mysql-data` 到容器内的 `/var/lib/mysql` 目录，同样设置容器在退出时自动重启。

### 3. 常用命令

#### 启动服务

在包含 `docker-compose.yml` 文件的目录下，使用以下命令启动所有服务：

```bash
docker-compose up -d
```

- `-d` 选项表示以守护进程模式运行，即服务在后台启动。

#### 查看服务状态

```bash
docker-compose ps
```

该命令会显示所有服务的运行状态、端口映射等信息。

#### 查看服务日志

```bash
docker-compose logs
```

如果需要查看某个特定服务的日志，可以指定服务名：

```bash
docker-compose logs web
```

#### 停止服务

```bash
docker-compose stop
```

该命令会停止所有正在运行的服务，但不会删除容器。

#### 停止并删除服务

```bash
docker-compose down
```

该命令会停止并删除所有服务的容器、网络和卷（除非卷是外部创建的）。

#### 构建服务镜像

如果服务使用的是自定义的 Dockerfile 构建镜像，可以使用以下命令构建：

```bash
docker-compose build
```

如果只需要构建某个特定服务的镜像，可以指定服务名：

```bash
docker-compose build web
```

#### 重启服务

```bash
docker-compose restart
```

同样，如果只需要重启某个特定服务，可以指定服务名：

```bash
docker-compose restart web
```

### 4. 扩展服务

可以使用 `scale` 命令扩展某个服务的容器数量：

```bash
docker-compose scale web=3
```

上述命令会将 `web` 服务的容器数量扩展到 3 个。

### 5. 环境变量

在 `docker-compose.yml` 文件中可以使用环境变量，有两种方式：

#### 在 `docker-compose.yml` 文件中直接使用

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    environment:
      - APP_ENV=production
```

#### 使用 `.env` 文件

在项目根目录下创建一个 `.env` 文件，内容如下：

```plaintext
APP_ENV=production
```

然后在 `docker-compose.yml` 文件中引用：

收起

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    environment:
      - APP_ENV=${APP_ENV}
```