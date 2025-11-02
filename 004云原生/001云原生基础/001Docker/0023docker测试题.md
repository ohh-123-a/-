# Docker 测试题

## 选择题 (40 题)

1. Docker 是一种什么类型的虚拟化技术？
   - A. 硬件虚拟化
   - B. 操作系统虚拟化
   - C. 应用程序虚拟化
   - D. 网络虚拟化
   - **答案：B**
   - **解析：Docker 是一种操作系统级别的虚拟化技术，它通过容器来隔离应用程序及其依赖。**

2. Docker 镜像和容器的关系是什么？
   - A. 镜像是容器的运行时实例
   - B. 容器是镜像的运行时实例
   - C. 镜像和容器是同一概念
   - D. 镜像和容器没有关系
   - **答案：B**
   - **解析：镜像是静态的模板，容器是镜像的运行时实例。**

3. 以下哪个命令用于从 Docker Hub 拉取镜像？
   - A. `docker pull`
   - B. `docker push`
   - C. `docker build`
   - D. `docker run`
   - **答案：A**
   - **解析：`docker pull` 命令用于从 Docker Hub 或其他镜像仓库拉取镜像。**

4. 以下哪个命令用于构建 Docker 镜像？
   - A. `docker build`
   - B. `docker run`
   - C. `docker create`
   - D. `docker start`
   - **答案：A**
   - **解析：`docker build` 命令用于根据 Dockerfile 构建镜像。**

5. Dockerfile 中的 `FROM` 指令的作用是什么？
   - A. 指定基础镜像
   - B. 指定容器启动命令
   - C. 指定容器端口
   - D. 指定容器网络
   - **答案：A**
   - **解析：`FROM` 指令用于指定基础镜像，后续的指令将基于该镜像进行构建。**

6. 以下哪个命令用于启动一个容器？
   - A. `docker start`
   - B. `docker run`
   - C. `docker create`
   - D. `docker build`
   - **答案：B**
   - **解析：`docker run` 命令用于创建并启动一个容器。**

7. 以下哪个命令用于查看正在运行的容器？
   - A. `docker ps`
   - B. `docker images`
   - C. `docker logs`
   - D. `docker inspect`
   - **答案：A**
   - **解析：`docker ps` 命令用于查看正在运行的容器。**

8. 以下哪个命令用于查看所有容器（包括已停止的）？
   - A. `docker ps -a`
   - B. `docker ps`
   - C. `docker images`
   - D. `docker logs`
   - **答案：A**
   - **解析：`docker ps -a` 命令用于查看所有容器，包括已停止的。**

9. 以下哪个命令用于删除一个容器？
   - A. `docker rm`
   - B. `docker rmi`
   - C. `docker delete`
   - D. `docker remove`
   - **答案：A**
   - **解析：`docker rm` 命令用于删除一个容器。**

10. 以下哪个命令用于删除一个镜像？
    - A. `docker rmi`
    - B. `docker rm`
    - C. `docker delete`
    - D. `docker remove`
    - **答案：A**
    - **解析：`docker rmi` 命令用于删除一个镜像。**

11. 以下哪个命令用于查看容器的日志？
    - A. `docker logs`
    - B. `docker ps`
    - C. `docker inspect`
    - D. `docker exec`
    - **答案：A**
    - **解析：`docker logs` 命令用于查看容器的日志。**

12. 以下哪个命令用于进入正在运行的容器？
    - A. `docker exec`
    - B. `docker attach`
    - C. `docker run`
    - D. `docker start`
    - **答案：A**
    - **解析：`docker exec` 命令用于在正在运行的容器中执行命令。**

13. 以下哪个命令用于将容器提交为镜像？
    - A. `docker commit`
    - B. `docker push`
    - C. `docker build`
    - D. `docker save`
    - **答案：A**
    - **解析：`docker commit` 命令用于将容器提交为镜像。**

14. 以下哪个命令用于将镜像保存为 tar 文件？
    - A. `docker save`
    - B. `docker load`
    - C. `docker export`
    - D. `docker import`
    - **答案：A**
    - **解析：`docker save` 命令用于将镜像保存为 tar 文件。**

15. 以下哪个命令用于从 tar 文件加载镜像？
    - A. `docker load`
    - B. `docker save`
    - C. `docker export`
    - D. `docker import`
    - **答案：A**
    - **解析：`docker load` 命令用于从 tar 文件加载镜像。**

16. 以下哪个命令用于将容器导出为 tar 文件？
    - A. `docker export`
    - B. `docker save`
    - C. `docker load`
    - D. `docker import`
    - **答案：A**
    - **解析：`docker export` 命令用于将容器导出为 tar 文件。**

17. 以下哪个命令用于从 tar 文件导入容器？
    - A. `docker import`
    - B. `docker export`
    - C. `docker save`
    - D. `docker load`
    - **答案：A**
    - **解析：`docker import` 命令用于从 tar 文件导入容器。**

18. 以下哪个命令用于查看 Docker 版本信息？
    - A. `docker version`
    - B. `docker info`
    - C. `docker inspect`
    - D. `docker logs`
    - **答案：A**
    - **解析：`docker version` 命令用于查看 Docker 版本信息。**

19. 以下哪个命令用于查看 Docker 系统信息？
    - A. `docker info`
    - B. `docker version`
    - C. `docker inspect`
    - D. `docker logs`
    - **答案：A**
    - **解析：`docker info` 命令用于查看 Docker 系统信息。**

20. 以下哪个命令用于查看镜像的详细信息？
    - A. `docker inspect`
    - B. `docker info`
    - C. `docker version`
    - D. `docker logs`
    - **答案：A**
    - **解析：`docker inspect` 命令用于查看镜像或容器的详细信息。**

21. 以下哪个命令用于将镜像推送到 Docker Hub？
    - A. `docker push`
    - B. `docker pull`
    - C. `docker build`
    - D. `docker run`
    - **答案：A**
    - **解析：`docker push` 命令用于将镜像推送到 Docker Hub 或其他镜像仓库。**

22. 以下哪个命令用于从 Dockerfile 构建镜像？
    - A. `docker build`
    - B. `docker run`
    - C. `docker create`
    - D. `docker start`
    - **答案：A**
    - **解析：`docker build` 命令用于根据 Dockerfile 构建镜像。**

23. 以下哪个命令用于停止一个正在运行的容器？
    - A. `docker stop`
    - B. `docker kill`
    - C. `docker rm`
    - D. `docker rmi`
    - **答案：A**
    - **解析：`docker stop` 命令用于停止一个正在运行的容器。**

24. 以下哪个命令用于强制停止一个正在运行的容器？
    - A. `docker kill`
    - B. `docker stop`
    - C. `docker rm`
    - D. `docker rmi`
    - **答案：A**
    - **解析：`docker kill` 命令用于强制停止一个正在运行的容器。**

25. 以下哪个命令用于查看容器的资源使用情况？
    - A. `docker stats`
    - B. `docker logs`
    - C. `docker inspect`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker stats` 命令用于查看容器的资源使用情况。**

26. 以下哪个命令用于查看容器的网络配置？
    - A. `docker network inspect`
    - B. `docker inspect`
    - C. `docker logs`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker network inspect` 命令用于查看容器的网络配置。**

27. 以下哪个命令用于创建一个新的 Docker 网络？
    - A. `docker network create`
    - B. `docker network inspect`
    - C. `docker network ls`
    - D. `docker network rm`
    - **答案：A**
    - **解析：`docker network create` 命令用于创建一个新的 Docker 网络。**

28. 以下哪个命令用于列出所有 Docker 网络？
    - A. `docker network ls`
    - B. `docker network inspect`
    - C. `docker network create`
    - D. `docker network rm`
    - **答案：A**
    - **解析：`docker network ls` 命令用于列出所有 Docker 网络。**

29. 以下哪个命令用于删除一个 Docker 网络？
    - A. `docker network rm`
    - B. `docker network inspect`
    - C. `docker network create`
    - D. `docker network ls`
    - **答案：A**
    - **解析：`docker network rm` 命令用于删除一个 Docker 网络。**

30. 以下哪个命令用于查看容器的详细信息？
    - A. `docker inspect`
    - B. `docker logs`
    - C. `docker ps`
    - D. `docker stats`
    - **答案：A**
    - **解析：`docker inspect` 命令用于查看容器的详细信息。**

31. 以下哪个命令用于查看容器的日志？
    - A. `docker logs`
    - B. `docker inspect`
    - C. `docker ps`
    - D. `docker stats`
    - **答案：A**
    - **解析：`docker logs` 命令用于查看容器的日志。**

32. 以下哪个命令用于查看容器的资源使用情况？
    - A. `docker stats`
    - B. `docker logs`
    - C. `docker inspect`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker stats` 命令用于查看容器的资源使用情况。**

33. 以下哪个命令用于查看容器的网络配置？
    - A. `docker network inspect`
    - B. `docker inspect`
    - C. `docker logs`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker network inspect` 命令用于查看容器的网络配置。**

34. 以下哪个命令用于创建一个新的 Docker 网络？
    - A. `docker network create`
    - B. `docker network inspect`
    - C. `docker network ls`
    - D. `docker network rm`
    - **答案：A**
    - **解析：`docker network create` 命令用于创建一个新的 Docker 网络。**

35. 以下哪个命令用于列出所有 Docker 网络？
    - A. `docker network ls`
    - B. `docker network inspect`
    - C. `docker network create`
    - D. `docker network rm`
    - **答案：A**
    - **解析：`docker network ls` 命令用于列出所有 Docker 网络。**

36. 以下哪个命令用于删除一个 Docker 网络？
    - A. `docker network rm`
    - B. `docker network inspect`
    - C. `docker network create`
    - D. `docker network ls`
    - **答案：A**
    - **解析：`docker network rm` 命令用于删除一个 Docker 网络。**

37. 以下哪个命令用于查看容器的详细信息？
    - A. `docker inspect`
    - B. `docker logs`
    - C. `docker ps`
    - D. `docker stats`
    - **答案：A**
    - **解析：`docker inspect` 命令用于查看容器的详细信息。**

38. 以下哪个命令用于查看容器的日志？
    - A. `docker logs`
    - B. `docker inspect`
    - C. `docker ps`
    - D. `docker stats`
    - **答案：A**
    - **解析：`docker logs` 命令用于查看容器的日志。**

39. 以下哪个命令用于查看容器的资源使用情况？
    - A. `docker stats`
    - B. `docker logs`
    - C. `docker inspect`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker stats` 命令用于查看容器的资源使用情况。**

40. 以下哪个命令用于查看容器的网络配置？
    - A. `docker network inspect`
    - B. `docker inspect`
    - C. `docker logs`
    - D. `docker ps`
    - **答案：A**
    - **解析：`docker network inspect` 命令用于查看容器的网络配置。**

## 填空题 (20 题)

1. Docker 镜像的默认存储路径是 `/var/lib/docker`。
2. Docker 容器的默认存储路径是 `/var/lib/docker/containers`。
3. Dockerfile 中的 `CMD` 指令用于指定容器启动时执行的命令。
4. Dockerfile 中的 `ENTRYPOINT` 指令用于指定容器启动时执行的命令。
5. Dockerfile 中的 `EXPOSE` 指令用于指定容器监听的端口。
6. Dockerfile 中的 `ENV` 指令用于设置环境变量。
7. Dockerfile 中的 `WORKDIR` 指令用于设置工作目录。
8. Dockerfile 中的 `COPY` 指令用于将文件从主机复制到容器。
9. Dockerfile 中的 `ADD` 指令用于将文件从主机复制到容器。
10. Dockerfile 中的 `RUN` 指令用于在构建镜像时执行命令。
11. Dockerfile 中的 `VOLUME` 指令用于创建挂载点。
12. Dockerfile 中的 `USER` 指令用于指定运行容器的用户。
13. Dockerfile 中的 `LABEL` 指令用于添加元数据。
14. Dockerfile 中的 `ARG` 指令用于定义构建时的变量。
15. Dockerfile 中的 `ONBUILD` 指令用于在子镜像构建时执行命令。
16. Dockerfile 中的 `HEALTHCHECK` 指令用于检查容器的健康状态。
17. Dockerfile 中的 `SHELL` 指令用于指定默认的 shell。
18. Dockerfile 中的 `STOPSIGNAL` 指令用于指定停止容器的信号。
19. Dockerfile 中的 `MAINTAINER` 指令用于指定镜像的维护者。
20. Dockerfile 中的 `FROM` 指令用于指定基础镜像。

## 判断题 (20 题)

1. Docker 容器是轻量级的虚拟化技术。 (正确)
2. Docker 容器与虚拟机相比，启动速度更快。 (正确)
3. Docker 容器与虚拟机相比，资源占用更少。 (正确)
4. Docker 容器与虚拟机相比，隔离性更强。 (错误)
5. Docker 容器与虚拟机相比，安全性更高。 (错误)
6. Docker 容器与虚拟机相比，性能更好。 (正确)
7. Docker 容器与虚拟机相比，可移植性更好。 (正确)
8. Docker 容器与虚拟机相比，可扩展性更好。 (正确)
9. Docker 容器与虚拟机相比，可管理性更好。 (正确)
10. Docker 容器与虚拟机相比，可维护性更好。 (正确)
11. Docker 容器与虚拟机相比，可监控性更好。 (正确)
12. Docker 容器与虚拟机相比，可调试性更好。 (正确)
13. Docker 容器与虚拟机相比，可部署性更好。 (正确)
14. Docker 容器与虚拟机相比，可测试性更好。 (正确)
15. Docker 容器与虚拟机相比，可扩展性更好。 (正确)
16. Docker 容器与虚拟机相比，可管理性更好。 (正确)
17. Docker 容器与虚拟机相比，可维护性更好。 (正确)
18. Docker 容器与虚拟机相比，可监控性更好。 (正确)
19. Docker 容器与虚拟机相比，可调试性更好。 (正确)
20. Docker 容器与虚拟机相比，可部署性更好。 (正确)

## 简答题 (10 题)

1. 什么是 Docker 容器？
   - **答案：Docker 容器是一种轻量级的虚拟化技术，它通过容器来隔离应用程序及其依赖。**

2. Docker 容器与虚拟机有什么区别？
   - **答案：Docker 容器与虚拟机相比，启动速度更快，资源占用更少，性能更好，可移植性更好，可扩展性更好，可管理性更好，可维护性更好，可监控性更好，可调试性更好，可部署性更好，可测试性更好。**

3. Docker 镜像和容器的关系是什么？
   - **答案：镜像是静态的模板，容器是镜像的运行时实例。**

4. Dockerfile 的作用是什么？
   - **答案：Dockerfile 是一个文本文件，用于定义如何构建 Docker 镜像。**

5. Dockerfile 中的 `FROM` 指令的作用是什么？
   - **答案：`FROM` 指令用于指定基础镜像，后续的指令将基于该镜像进行构建。**

6. Dockerfile 中的 `CMD` 指令的作用是什么？
   - **答案：`CMD` 指令用于指定容器启动时执行的命令。**

7. Dockerfile 中的 `ENTRYPOINT` 指令的作用是什么？
   - **答案：`ENTRYPOINT` 指令用于指定容器启动时执行的命令。**

8. Dockerfile 中的 `EXPOSE` 指令的作用是什么？
   - **答案：`EXPOSE` 指令用于指定容器监听的端口。**

9. Dockerfile 中的 `ENV` 指令的作用是什么？
   - **答案：`ENV` 指令用于设置环境变量。**

10. Dockerfile 中的 `WORKDIR` 指令的作用是什么？
    - **答案：`WORKDIR` 指令用于设置工作目录。**

## 代码题 

1. 编写一个 Dockerfile，基于 `ubuntu:latest` 镜像，安装 `nginx` 并暴露 80 端口。
   - **答案：**
     ```dockerfile
     FROM ubuntu:latest
     RUN apt-get update && apt-get install -y nginx
     EXPOSE 