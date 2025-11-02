## docker-compse详解

`Docker Compose` 是 Docker 官方推出的一个工具，用于定义和运行多容器 Docker 应用程序。借助单个 `docker-compose.yml` 文件，你可以对应用的服务、网络以及卷等进行配置，然后使用一条命令来启动或停止所有服务。下面为你详细介绍 `Docker Compose`：

### 安装

不同操作系统的安装方式有所不同，以 Linux 为例，可使用以下命令进行安装：

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.15.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### `docker-compose.yml` 文件

`docker-compose.yml` 是 `Docker Compose` 的核心配置文件，下面是一个简单示例：

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - app
  app:
    build: .
    environment:
      - DB_HOST=db
      - DB_USER=user
      - DB_PASSWORD=password
    depends_on:
      - db
  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=mydb
      - MYSQL_USER=user
      - MYSQL_PASSWORD=password
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
```

### 配置项说明

1. **`version`**：指定 `docker-compose.yml` 文件的版本，不同版本支持的功能有所差异。

2. `services`

   ：定义多个服务，每个服务相当于一个容器。

   - **`image`**：指定服务使用的 Docker 镜像。
   - **`build`**：若没有现成镜像，可通过 `build` 选项指定 Dockerfile 所在路径来构建镜像。
   - **`ports`**：将容器端口映射到宿主机端口。
   - **`volumes`**：将宿主机目录或文件挂载到容器内。
   - **`environment`**：设置容器内的环境变量。
   - **`depends_on`**：定义服务之间的依赖关系，确保依赖的服务先启动。

3. **`volumes`**：定义数据卷，用于持久化存储容器的数据。

### 常用命令

- **`docker-compose up`**：启动所有服务。使用 `-d` 选项可在后台运行，例如：`docker-compose up -d`。
- **`docker-compose down`**：停止并移除所有服务的容器、网络和数据卷。
- **`docker-compose ps`**：查看服务的运行状态。
- **`docker-compose logs`**：查看服务的日志输出。
- **`docker-compose build`**：构建或重新构建服务的镜像。

### 工作流程

1. **编写 `docker-compose.yml` 文件**：根据应用需求定义服务、网络和卷等。
2. **构建镜像**：使用 `docker-compose build` 命令构建所需的镜像。
3. **启动服务**：使用 `docker-compose up` 命令启动所有服务。
4. **管理服务**：使用 `docker-compose` 的其他命令进行服务的停止、重启、查看日志等操作。

### 优势

- **简化配置**：将多个容器的配置集中在一个文件中，方便管理和维护。
- **快速部署**：通过一条命令即可启动整个应用的所有服务。
- **环境一致性**：确保开发、测试和生产环境的一致性。