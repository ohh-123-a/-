# docker 基础用法

Docker 是一个用于开发、部署和运行应用程序的开源平台，它使用容器化技术将应用及其依赖打包成一个独立的容器。以下是 Docker 的一些基础用法：

### 安装 Docker

在不同的操作系统上安装 Docker 的方式有所不同，以下以常见的 Linux 系统（如 Ubuntu）为例：

```bash
# 更新系统软件包列表
sudo apt update
# 安装必要的依赖包
sudo apt install apt-transport-https ca-certificates curl software-properties-common
# 添加 Docker 的官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
# 添加 Docker 软件源
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# 更新软件包列表
sudo apt update
# 安装 Docker 引擎
sudo apt install docker-ce docker-ce-cli containerd.io
# 验证 Docker 是否安装成功
sudo docker run hello-world
```

### 镜像操作

#### 搜索镜像

可以使用 `docker search` 命令在 Docker Hub 上搜索所需的镜像，例如搜索 `nginx` 镜像：

因为centos这些镜像有多个版本。m y s q l 5.7.44. mysql:8.0.22....

nginx也有1.26 1.22

```bash
docker search nginx
```

#### 拉取镜像

使用 `docker pull` 命令从 Docker Hub 或其他镜像仓库拉取镜像，例如拉取 `nginx` 镜像：

如果在拉取的镜像名后不加冒号和版本，默认下载latest最新版本

```bash
docker pull nginx
```

#### 查看本地镜像

使用 `docker images` 命令查看本地已有的镜像：

```bash
docker images
```

#### 删除本地镜像

使用 `docker rmi` 命令删除本地镜像，例如删除 `nginx` 镜像：

```bash
docker rmi nginx
```

### 容器操作

#### 创建并运行容器

使用 `docker run` 命令创建并运行一个容器，例如运行 `nginx` 容器：

```bash
docker run -d -p 80:80 nginx
```

- `-d`：表示以守护进程模式运行容器，即容器在后台运行。
- `-p 80:80`：表示将容器的 80 端口映射到主机的 80 端口。

#### 查看运行中的容器

使用 `docker ps` 命令查看当前正在运行的容器：

```bash
docker ps
```

若要查看所有容器（包括已停止的容器），可以使用 `docker ps -a` 命令。

#### 停止容器

使用 `docker stop` 命令停止正在运行的容器，例如停止 `nginx` 容器：

```bash
docker stop <容器 ID 或容器名称>
```

#### 启动已停止的容器

使用 `docker start` 命令启动已停止的容器，例如启动 `nginx` 容器：

```bash
docker start <容器 ID 或容器名称>
```

#### 删除容器

使用 `docker rm` 命令删除容器，例如删除 `nginx` 容器：

```bash
docker rm <容器 ID 或容器名称>
```

### 容器内操作

#### 进入容器

使用 `docker exec` 命令进入正在运行的容器，例如进入 `nginx` 容器的 shell：

```bash
docker exec -it <容器 ID 或容器名称> /bin/bash
```

- `-i`：表示以交互模式运行命令。
- `-t`：表示为命令分配一个伪终端。

#### 查看容器日志

使用 `docker logs` 命令查看容器的日志信息，例如查看 `nginx` 容器的日志：

```bash
docker logs <容器 ID 或容器名称>
```

### 构建自定义镜像

可以使用 `Dockerfile` 来构建自定义镜像，以下是一个简单的 `Dockerfile` 示例：

```Dockerfile
# 使用基础镜像
FROM ubuntu:latest
# 安装必要的软件包
RUN apt update && apt install -y nginx
# 暴露端口
EXPOSE 80
# 启动 Nginx 服务
CMD ["nginx", "-g", "daemon off;"]
```





在包含 `Dockerfile` 的目录下，使用 `docker build` 命令构建镜像：

```bash
docker build -t my-nginx .
```





- `-t`：表示为镜像指定一个标签，格式为 `名称:版本`，这里的 `my-nginx` 是镜像的名称。
- `.`：表示使用当前目录下的 `Dockerfile` 进行构建。

以上就是 Docker 的一些基础用法，通过这些操作可以快速上手 Docker 并进行应用的开发、部署和管理。