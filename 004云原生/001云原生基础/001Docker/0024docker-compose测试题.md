# Docker-Compose 测试题

## 选择题（20题）

1. **Docker-Compose 文件默认的文件名是什么？**
   - A. docker-compose.yaml
   - B. docker-compose.yml
   - C. compose.yaml
   - D. compose.yml
   **答案：B**
   **解析：Docker-Compose 默认使用 `docker-compose.yml` 作为配置文件。**

2. **以下哪个命令用于启动 Docker-Compose 服务？**
   - A. docker-compose up
   - B. docker-compose start
   - C. docker-compose run
   - D. docker-compose build
   **答案：A**
   **解析：`docker-compose up` 用于启动并运行所有服务。**

3. **Docker-Compose 文件中 `version` 字段的作用是什么？**
   - A. 指定 Docker 版本
   - B. 指定 Docker-Compose 文件格式版本
   - C. 指定 Docker Engine 版本
   - D. 指定 Docker-Compose 工具版本
   **答案：B**
   **解析：`version` 字段用于指定 Docker-Compose 文件格式的版本。**

4. **以下哪个命令用于停止并删除 Docker-Compose 服务？**
   - A. docker-compose stop
   - B. docker-compose down
   - C. docker-compose rm
   - D. docker-compose kill
   **答案：B**
   **解析：`docker-compose down` 用于停止并删除容器、网络、卷等资源。**

5. **Docker-Compose 文件中 `services` 字段的作用是什么？**
   - A. 定义容器镜像
   - B. 定义服务
   - C. 定义网络
   - D. 定义卷
   **答案：B**
   **解析：`services` 字段用于定义 Docker-Compose 中的各个服务。**

6. **以下哪个命令用于查看 Docker-Compose 服务的日志？**
   - A. docker-compose logs
   - B. docker-compose log
   - C. docker-compose show-logs
   - D. docker-compose view-logs
   **答案：A**
   **解析：`docker-compose logs` 用于查看服务的日志输出。**

7. **Docker-Compose 文件中 `volumes` 字段的作用是什么？**
   - A. 定义容器挂载的卷
   - B. 定义容器的网络
   - C. 定义容器的环境变量
   - D. 定义容器的端口映射
   **答案：A**
   **解析：`volumes` 字段用于定义容器挂载的卷。**

8. **以下哪个命令用于构建 Docker-Compose 服务？**
   - A. docker-compose build
   - B. docker-compose create
   - C. docker-compose make
   - D. docker-compose construct
   **答案：A**
   **解析：`docker-compose build` 用于构建服务所需的镜像。**

9. **Docker-Compose 文件中 `networks` 字段的作用是什么？**
   - A. 定义容器的网络
   - B. 定义容器的卷
   - C. 定义容器的环境变量
   - D. 定义容器的端口映射
   **答案：A**
   **解析：`networks` 字段用于定义容器的网络配置。**

10. **以下哪个命令用于重启 Docker-Compose 服务？**
    - A. docker-compose restart
    - B. docker-compose reload
    - C. docker-compose reset
    - D. docker-compose refresh
    **答案：A**
    **解析：`docker-compose restart` 用于重启服务。**

11. **Docker-Compose 文件中 `environment` 字段的作用是什么？**
    - A. 定义容器的环境变量
    - B. 定义容器的网络
    - C. 定义容器的卷
    - D. 定义容器的端口映射
    **答案：A**
    **解析：`environment` 字段用于定义容器的环境变量。**

12. **以下哪个命令用于查看 Docker-Compose 服务的状态？**
    - A. docker-compose ps
    - B. docker-compose status
    - C. docker-compose show
    - D. docker-compose view
    **答案：A**
    **解析：`docker-compose ps` 用于查看服务的状态。**

13. **Docker-Compose 文件中 `ports` 字段的作用是什么？**
    - A. 定义容器的端口映射
    - B. 定义容器的网络
    - C. 定义容器的卷
    - D. 定义容器的环境变量
    **答案：A**
    **解析：`ports` 字段用于定义容器的端口映射。**

14. **以下哪个命令用于删除 Docker-Compose 服务的容器？**
    - A. docker-compose rm
    - B. docker-compose delete
    - C. docker-compose remove
    - D. docker-compose destroy
    **答案：A**
    **解析：`docker-compose rm` 用于删除已停止的容器。**

15. **Docker-Compose 文件中 `depends_on` 字段的作用是什么？**
    - A. 定义服务之间的依赖关系
    - B. 定义容器的网络
    - C. 定义容器的卷
    - D. 定义容器的环境变量
    **答案：A**
    **解析：`depends_on` 字段用于定义服务之间的启动顺序依赖关系。**

16. **以下哪个命令用于查看 Docker-Compose 服务的配置？**
    - A. docker-compose config
    - B. docker-compose show-config
    - C. docker-compose view-config
    - D. docker-compose inspect
    **答案：A**
    **解析：`docker-compose config` 用于查看服务的配置。**

17. **Docker-Compose 文件中 `build` 字段的作用是什么？**
    - A. 定义如何构建镜像
    - B. 定义容器的网络
    - C. 定义容器的卷
    - D. 定义容器的环境变量
    **答案：A**
    **解析：`build` 字段用于定义如何构建镜像。**

18. **以下哪个命令用于进入 Docker-Compose 服务的容器？**
    - A. docker-compose exec
    - B. docker-compose enter
    - C. docker-compose run
    - D. docker-compose attach
    **答案：A**
    **解析：`docker-compose exec` 用于在运行的容器中执行命令。**

19. **Docker-Compose 文件中 `restart` 字段的作用是什么？**
    - A. 定义容器的重启策略
    - B. 定义容器的网络
    - C. 定义容器的卷
    - D. 定义容器的环境变量
    **答案：A**
    **解析：`restart` 字段用于定义容器的重启策略。**

20. **以下哪个命令用于查看 Docker-Compose 服务的版本？**
    - A. docker-compose version
    - B. docker-compose --version
    - C. docker-compose show-version
    - D. docker-compose view-version
    **答案：B**
    **解析：`docker-compose --version` 用于查看 Docker-Compose 的版本。**

## 填空题（10题）

1. **Docker-Compose 文件的默认文件名是 `__________`。**
   **答案：docker-compose.yml**

2. **`docker-compose up` 命令用于 `__________` 服务。**
   **答案：启动**

3. **Docker-Compose 文件中 `version` 字段用于指定 `__________` 版本。**
   **答案：文件格式**

4. **`docker-compose down` 命令用于 `__________` 服务。**
   **答案：停止并删除**

5. **Docker-Compose 文件中 `services` 字段用于定义 `__________`。**
   **答案：服务**

6. **`docker-compose logs` 命令用于查看 `__________`。**
   **答案：日志**

7. **Docker-Compose 文件中 `volumes` 字段用于定义 `__________`。**
   **答案：卷**

8. **`docker-compose build` 命令用于 `__________` 镜像。**
   **答案：构建**

9. **Docker-Compose 文件中 `networks` 字段用于定义 `__________`。**
   **答案：网络**

10. **`docker-compose restart` 命令用于 `__________` 服务。**
    **答案：重启**

## 判断题（10题）

1. **Docker-Compose 文件的默认文件名是 `docker-compose.yaml`。**
   **答案：错误**
   **解析：默认文件名是 `docker-compose.yml`。**

2. **`docker-compose up` 命令用于启动服务。**
   **答案：正确**
   **解析：`docker-compose up` 用于启动并运行所有服务。**

3. **Docker-Compose 文件中 `version` 字段用于指定 Docker 版本。**
   **答案：错误**
   **解析：`version` 字段用于指定 Docker-Compose 文件格式的版本。**

4. **`docker-compose down` 命令用于停止并删除服务。**
   **答案：正确**
   **解析：`docker-compose down` 用于停止并删除容器、网络、卷等资源。**

5. **Docker-Compose 文件中 `services` 字段用于定义容器镜像。**
   **答案：错误**
   **解析：`services` 字段用于定义服务。**

6. **`docker-compose logs` 命令用于查看服务的日志。**
   **答案：正确**
   **解析：`docker-compose logs` 用于查看服务的日志输出。**

7. **Docker-Compose 文件中 `volumes` 字段用于定义容器的网络。**
   **答案：错误**
   **解析：`volumes` 字段用于定义容器挂载的卷。**

8. **`docker-compose build` 命令用于构建服务所需的镜像。**
   **答案：正确**
   **解析：`docker-compose build` 用于构建服务所需的镜像。**

9. **Docker-Compose 文件中 `networks` 字段用于定义容器的卷。**
   **答案：错误**
   **解析：`networks` 字段用于定义容器的网络配置。**

10. **`docker-compose restart` 命令用于重启服务。**
    **答案：正确**
    **解析：`docker-compose restart` 用于重启服务。**

## 简答题（5题）

1. **请简述 Docker-Compose 的作用。**
   **答案：Docker-Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过一个 YAML 文件配置应用的服务、网络、卷等，并使用简单的命令管理整个应用的生命周期。**

2. **如何在 Docker-Compose 文件中定义多个服务？**
   **答案：在 `docker-compose.yml` 文件中，使用 `services` 字段定义多个服务，每个服务可以指定镜像、端口映射、环境变量、卷等配置。**

3. **如何查看 Docker-Compose 服务的日志？**
   **答案：使用 `docker-compose logs` 命令可以查看服务的日志输出。**

4. **如何在 Docker-Compose 文件中定义服务之间的依赖关系？**
   **答案：在 `docker-compose.yml` 文件中，使用 `depends_on` 字段定义服务之间的依赖关系，确保服务按顺序启动。**

5. **如何构建 Docker-Compose 服务的镜像？**
   **答案：使用 `docker-compose build` 命令可以构建服务所需的镜像。**

## 代码题（2题）

1. **编写一个简单的 `docker-compose.yml` 文件，包含一个 `web` 服务和一个 `db` 服务。`web` 服务使用 `nginx` 镜像，`db` 服务使用 `mysql` 镜像。**
   **答案：**
   ```yaml
   version: '3'
   services:
     web:
       image: nginx
       ports:
         - "80:80"
     db:
       image: mysql
       environment:
         MYSQL_ROOT_PASSWORD: example

2. **编写一个 `docker-compose.yml` 文件，定义一个 `app` 服务，使用本地 Dockerfile 构建镜像，并挂载一个本地目录到容器的 `/app` 目录。**
   **答案：**
   ```yaml
   version: '3'
   services:
     app:
       build: .
       volumes:
         - ./local_dir:/app

---

