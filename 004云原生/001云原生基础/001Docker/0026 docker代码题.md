# Docker 运维代码题

## 题目 1: 列出所有正在运行的容器
**题目**: 使用 Docker 命令列出所有正在运行的容器。

**答案**:
```bash
docker ps
```

**解析**:
- `docker ps` 命令用于列出当前正在运行的容器。
- 默认情况下，它只显示正在运行的容器。如果要查看所有容器（包括已停止的），可以使用 `docker ps -a`。

---

## 题目 2: 停止一个正在运行的容器
**题目**: 使用 Docker 命令停止一个正在运行的容器，容器 ID 为 `abc123`。

**答案**:
```bash
docker stop abc123
```

**解析**:
- `docker stop` 命令用于停止一个正在运行的容器。
- 容器 ID 可以是完整的 ID 或前几个字符，只要能够唯一标识容器即可。

---

## 题目 3: 删除一个已停止的容器
**题目**: 使用 Docker 命令删除一个已停止的容器，容器 ID 为 `def456`。

**答案**:
```bash
docker rm def456
```

**解析**:
- `docker rm` 命令用于删除一个已停止的容器。
- 如果容器正在运行，需要先停止容器，或者使用 `docker rm -f` 强制删除。

---

## 题目 4: 拉取一个 Docker 镜像
**题目**: 使用 Docker 命令从 Docker Hub 拉取 `nginx` 镜像。

**答案**:
```bash
docker pull nginx
```

**解析**:
- `docker pull` 命令用于从 Docker Hub 或其他镜像仓库拉取镜像。
- 默认情况下，它会拉取最新的 `latest` 标签的镜像。

---

## 题目 5: 运行一个容器并映射端口
**题目**: 使用 Docker 命令运行一个 `nginx` 容器，并将宿主机的 `8080` 端口映射到容器的 `80` 端口。

**答案**:
```bash
docker run -d -p 8080:80 nginx
```

**解析**:
- `docker run` 命令用于运行一个容器。
- `-d` 选项表示在后台运行容器（detached 模式）。
- `-p 8080:80` 表示将宿主机的 `8080` 端口映射到容器的 `80` 端口。

---

## 题目 6: 查看容器的日志
**题目**: 使用 Docker 命令查看容器 ID 为 `ghi789` 的日志。

**答案**:
```bash
docker logs ghi789
```

**解析**:
- `docker logs` 命令用于查看容器的日志输出。
- 可以使用 `-f` 选项来实时跟踪日志输出。

---

## 题目 7: 进入正在运行的容器
**题目**: 使用 Docker 命令进入容器 ID 为 `jkl012` 的交互式 shell。

**答案**:
```bash
docker exec -it jkl012 /bin/bash
```

**解析**:
- `docker exec` 命令用于在正在运行的容器中执行命令。
- `-it` 选项表示以交互式终端模式运行命令。
- `/bin/bash` 是要在容器中执行的命令，通常是启动一个 shell。

---

## 题目 8: 构建一个 Docker 镜像
**题目**: 使用 Docker 命令构建一个名为 `myapp` 的镜像，Dockerfile 位于当前目录。

**答案**:
```bash
docker build -t myapp .
```

**解析**:
- `docker build` 命令用于根据 Dockerfile 构建镜像。
- `-t myapp` 选项指定镜像的名称为 `myapp`。
- `.` 表示 Dockerfile 位于当前目录。

---

## 题目 9: 查看所有 Docker 镜像
**题目**: 使用 Docker 命令列出所有本地存储的 Docker 镜像。

**答案**:
```bash
docker images
```

**解析**:
- `docker images` 命令用于列出本地存储的所有 Docker 镜像。
- 输出包括镜像名称、标签、镜像 ID、创建时间和大小等信息。

---

## 题目 10: 删除一个 Docker 镜像
**题目**: 使用 Docker 命令删除镜像 ID 为 `mno345` 的镜像。

**答案**:
```bash
docker rmi mno345
```

**解析**:
- `docker rmi` 命令用于删除本地存储的 Docker 镜像。
- 如果镜像正在被容器使用，需要先删除相关容器。

---

## 题目 11: 查看 Docker 容器的资源使用情况
**题目**: 使用 Docker 命令查看容器 ID 为 `pqr678` 的资源使用情况（CPU、内存等）。

**答案**:
```bash
docker stats pqr678
```

**解析**:
- `docker stats` 命令用于实时查看容器的资源使用情况。
- 输出包括 CPU 使用率、内存使用量、网络 I/O 等信息。

---

## 题目 12: 创建一个 Docker 网络
**题目**: 使用 Docker 命令创建一个名为 `mynetwork` 的自定义网络。

**答案**:
```bash
docker network create mynetwork
```

**解析**:
- `docker network create` 命令用于创建一个自定义的 Docker 网络。
- 自定义网络可以用于容器之间的通信。

---

## 题目 13: 将容器连接到自定义网络
**题目**: 使用 Docker 命令将容器 ID 为 `stu901` 连接到名为 `mynetwork` 的自定义网络。

**答案**:
```bash
docker network connect mynetwork stu901
```

**解析**:
- `docker network connect` 命令用于将容器连接到指定的网络。
- 容器可以同时连接到多个网络。

---

## 题目 14: 查看 Docker 网络信息
**题目**: 使用 Docker 命令查看所有 Docker 网络的详细信息。

**答案**:
```bash
docker network ls
```

**解析**:
- `docker network ls` 命令用于列出所有 Docker 网络。
- 输出包括网络 ID、名称、驱动程序和范围等信息。

---

## 题目 15: 删除一个 Docker 网络
**题目**: 使用 Docker 命令删除名为 `mynetwork` 的自定义网络。

**答案**:
```bash
docker network rm mynetwork
```

**解析**:
- `docker network rm` 命令用于删除一个 Docker 网络。
- 如果网络中有容器连接，需要先断开连接。

---

## 题目 16: 查看 Docker 容器的详细信息
**题目**: 使用 Docker 命令查看容器 ID 为 `vwx234` 的详细信息。

**答案**:
```bash
docker inspect vwx234
```

**解析**:
- `docker inspect` 命令用于查看 Docker 对象（如容器、镜像、网络等）的详细信息。
- 输出为 JSON 格式，包含容器的配置、网络设置、挂载点等信息。

---

## 题目 17: 导出 Docker 容器为 tar 文件
**题目**: 使用 Docker 命令将容器 ID 为 `yza567` 导出为 `container.tar` 文件。

**答案**:
```bash
docker export -o container.tar yza567
```

**解析**:
- `docker export` 命令用于将容器的文件系统导出为 tar 文件。
- `-o container.tar` 选项指定输出文件的名称。

---

## 题目 18: 导入 Docker 镜像
**题目**: 使用 Docker 命令从 `image.tar` 文件导入一个 Docker 镜像。

**答案**:
```bash
docker import image.tar myimage:latest
```

**解析**:
- `docker import` 命令用于从 tar 文件导入一个 Docker 镜像。
- `myimage:latest` 是导入后镜像的名称和标签。

---

## 题目 19: 查看 Docker 版本信息
**题目**: 使用 Docker 命令查看 Docker 客户端和服务器的版本信息。

**答案**:
```bash
docker version
```

**解析**:
- `docker version` 命令用于查看 Docker 客户端和服务器的版本信息。
- 输出包括客户端和服务器的版本号、API 版本、Go 版本等。

---

## 题目 20: 清理未使用的 Docker 对象
**题目**: 使用 Docker 命令清理未使用的镜像、容器、网络和构建缓存。

**答案**:
```bash
docker system prune -a
```

**解析**:
- `docker system prune` 命令用于清理未使用的 Docker 对象。
- `-a` 选项表示清理所有未使用的对象，包括未使用的镜像。
- 使用 `-f` 选项可以跳过确认提示。