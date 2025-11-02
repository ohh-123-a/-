docker 常用命令与配置

### 常用命令

#### 镜像相关命令

1. 搜索镜像
   - 用途：在 Docker Hub 上搜索符合关键词的镜像。
   - 命令：`docker search <镜像名称>`
   - 示例：`docker search nginx`
2. 拉取镜像
   - 用途：从 Docker Hub 或其他镜像仓库下载镜像到本地。
   - 命令：`docker pull <镜像名称>:<标签>`，若不指定标签，默认拉取 `latest` 标签的镜像。
   - 示例：`docker pull mysql:8.0`
3. 列出本地镜像
   - 用途：查看本地已下载的所有镜像。
   - 命令：`docker images`
   - 示例：执行后会显示镜像的仓库名、标签、镜像 ID、创建时间和大小等信息。
4. 删除本地镜像
   - 用途：移除本地不再需要的镜像。
   - 命令：`docker rmi <镜像 ID 或镜像名称:标签>`
   - 示例：`docker rmi nginx:latest`
5. 构建镜像
   - 用途：根据 `Dockerfile` 构建自定义镜像。
   - 命令：`docker build -t <镜像名称:标签> <上下文路径>`
   - 示例：`docker build -t myapp:1.0 .`，其中 `.` 表示当前目录为上下文路径。
6. 保存镜像
   - 用途：将本地镜像保存为一个文件，方便传输。
   - 命令：`docker save -o <保存的文件名> <镜像名称:标签>`
   - 示例：`docker save -o mynginx.tar nginx:latest`
7. 加载镜像
   - 用途：从保存的文件中加载镜像到本地。
   - 命令：`docker load -i <保存的文件名>`
   - 示例：`docker load -i mynginx.tar`

#### 容器相关命令

1. 创建并运行容器

   - 用途：基于指定镜像创建一个新的容器并启动它。

   - 命令：`docker run [选项] <镜像名称:标签> [命令] [参数]`

   - 示例：

     ```
     docker run -d -p 8080:80 --name mynginx nginx
     ```

     - `-d`：以守护进程模式运行容器。
     - `-p 8080:80`：将容器的 80 端口映射到主机的 8080 端口。
     - `--name mynginx`：为容器指定名称为 `mynginx`。

2. 列出运行中的容器

   - 用途：查看当前正在运行的容器。
   - 命令：`docker ps`
   - 示例：显示容器的 ID、名称、使用的镜像、创建时间、状态等信息。

3. 列出所有容器（包括已停止的）

   - 用途：查看所有容器，无论其运行状态如何。
   - 命令：`docker ps -a`

4. 停止容器

   - 用途：停止正在运行的容器。
   - 命令：`docker stop <容器 ID 或容器名称>`
   - 示例：`docker stop mynginx`

5. 启动已停止的容器

   - 用途：重新启动之前停止的容器。
   - 命令：`docker start <容器 ID 或容器名称>`
   - 示例：`docker start mynginx`

6. 重启容器

   - 用途：对正在运行的容器进行重启操作。
   - 命令：`docker restart <容器 ID 或容器名称>`
   - 示例：`docker restart mynginx`

7. 删除容器

   - 用途：移除不再需要的容器。容器必须先停止才能删除，若要强制删除运行中的容器，可使用 `-f` 选项。
   - 命令：`docker rm <容器 ID 或容器名称>`
   - 示例：`docker rm mynginx`

8. 进入容器

   - 用途：在运行的容器内部执行命令，常用于调试和管理。
   - 命令：`docker exec -it <容器 ID 或容器名称> <命令>`
   - 示例：`docker exec -it mynginx /bin/bash`，进入 `mynginx` 容器的 bash 终端。

9. 查看容器日志

   - 用途：查看容器的标准输出和错误输出信息。
   - 命令：`docker logs <容器 ID 或容器名称>`
   - 示例：`docker logs mynginx`

10. 查看容器资源使用情况

    - 用途：监控容器的 CPU、内存、网络等资源使用情况。
    - 命令：`docker stats <容器 ID 或容器名称>`
    - 示例：`docker stats mynginx`

#### 网络相关命令

1. 列出网络
   - 用途：查看 Docker 中创建的所有网络。
   - 命令：`docker network ls`
2. 创建网络
   - 用途：创建自定义的 Docker 网络。
   - 命令：`docker network create [选项] <网络名称>`
   - 示例：`docker network create mynetwork`
3. 连接容器到网络
   - 用途：将容器加入到指定的网络中。
   - 命令：`docker network connect <网络名称> <容器 ID 或容器名称>`
   - 示例：`docker network connect mynetwork mynginx`
4. 断开容器与网络的连接
   - 用途：将容器从指定网络中移除。
   - 命令：`docker network disconnect <网络名称> <容器 ID 或容器名称>`
   - 示例：`docker network disconnect mynetwork mynginx`

### 常用配置

#### Docker 守护进程配置

Docker 守护进程的配置文件通常位于 `/etc/docker/daemon.json`。以下是一些常见的配置项：

1. 镜像仓库地址
   - 用途：配置 Docker 从指定的镜像仓库拉取镜像。
   - 示例：

```json
{
    "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

1. 数据存储路径
   - 用途：指定 Docker 镜像、容器等数据的存储位置。
   - 示例：

```json
{
    "data-root": "/new/docker/data/path"
}
```

1. 日志驱动和日志大小限制
   - 用途：配置容器日志的记录方式和大小限制。
   - 示例：

```json
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

#### Dockerfile 配置

`Dockerfile` 用于构建自定义镜像，以下是一些常见的指令：

1. `FROM`
   - 用途：指定基础镜像。
   - 示例：`FROM ubuntu:20.04`
2. `RUN`
   - 用途：在容器内执行命令。
   - 示例：`RUN apt-get update && apt-get install -y nginx`
3. `COPY` 和 `ADD`
   - 用途：将本地文件复制到容器内。`COPY` 更简单直接，`ADD` 还支持自动解压压缩文件。
   - 示例：`COPY ./app /app`
4. `CMD` 和 `ENTRYPOINT`
   - 用途：指定容器启动时执行的命令。`CMD` 可以被 `docker run` 后面的命令覆盖，`ENTRYPOINT` 则不会。
   - 示例：

```Dockerfile
CMD ["nginx", "-g", "daemon off;"]
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```





1. `EXPOSE`
   - 用途：声明容器要监听的端口。
   - 示例：`EXPOSE 80`
2. `ENV`
   - 用途：设置环境变量。
   - 示例：`ENV MYSQL_ROOT_PASSWORD=123456`