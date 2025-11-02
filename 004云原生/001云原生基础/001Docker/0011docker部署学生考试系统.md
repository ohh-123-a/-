# docker 原生部署《学生考试系统》

```bash
# 部署数据库
# --name db 指定容器名称
# -v 挂载dis文件
docker run -itd --name db -v /root/pes/mysql/data/:/var/lib/mysql/ mysql:5.7.44
# --link db:db 添加链接
# --p32100:8080 绑定端口
# -v 挂载文件
docker run -itd --link db:db -p32100:8080 -v /root/pes/java/src/:/usr/local/tomcat/ tomcat:latest java -jar Project_ExamSystem-V1.0.0.war
# 部署nginx前端
docker run -itd -v /root/pes/web/dist/:/usr/share/nginx/html/ -p80:80 nginx:latest


```

步骤

1. 安装docker，配置镜像

2. 拉取镜像

   ```bash
   docker pull nginx
   docker pull tomcat
   docker pull mysql:5.7.44
   #或者
   docker load -i nginx.tar
   docker load -i tomcat.tar
   docker load -i mysql5.7.44.tar
   ```

   确保镜像列表中有以上的三个内容

   ```bash
   [root@docker ~]# docker images
   REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
   tomcat       latest    dc938bdaa895   2 weeks ago     511MB
   nginx        latest    b52e0b094bc0   4 weeks ago     192MB
   mysql        5.7.44    5107333e08a8   15 months ago   501MB
   ```

3. 先部署mysql

   ```bash
   # 直接开启mysql容器
   docker run mysql:5.7.44
   # 这种启动方式，是直接在前台启动，并且占用前台，而且无法交互，并且数据库没有初始化，因为没有提供环境变量MYSQL_ROOT_PASSWORD(在mariadb的镜像中，设置的密码变量是 MARIADB_ROOT_MSYQL)
   # 经过查询help 使用-v选项可以添加环境变量变量和环境变量列表
   docker run -e MYSQL_ROOT_PASSWORD=root mysql:5.7.44
   # 以上的容器启动，依然占用前台，并且无法交互
   # 查询help -i stuin -t tty -d background
   docker run -itd -e MYSQL_ROOT_PASSWORD=ROOT mysql:5.7.44
   # 查看容器
   docker ps
   # 移除无效的容器
   docker stop 容器id
   docker rm id1 id2 id3....
   
   # 如果希望在宿主机之外能访问到容器的服务，就需要将容器的端口映射到宿主机，当外部用户访问到宿主机的对应端口，会转发到容器的端口
   docker run -itd -e MYSQL_ROOT_PASSWORD=root -p3306:3306 mysql:5.7.44
   # -p 端口1:端口2 表示当外部用户访问宿主机的端口1时候，会自动转发到容器的端口2
   # -p端口3 没有指定宿主机映射的端口，宿主机会随机指定一个大于32767的端口（虽然说端口是65536，显得多，只要会出现冲突，我们都说他是稀缺资源）
   # 将sql文件放到容器内容，有2种方法
   # 1. docker cp
   # docker cp 宿主机上的资源 容器id:容器上的保存目录
   docker cp /root/pes/mysql/project_exam_system.sql 25:/sql
   
   # 2. 将导入的文件以挂载卷的方式添加到容器，这个需要在创建容器的时候添加
   docker run -tid -p3306 -e MYSQL_ROOT_PASSWORD=root -v /root/pes/mysql/:/sql/ mysql:5.7.44
   
   
   
   # 导入数据库sql
   mysql -uroot -proot -e 'create database if not exists prject_exam_system charset="utf8"'
   mysql -uroot -proot -e 'show databases'
   mysql -uroot -p project_exam_system < /sql/project_exam_system.sql
   mysql -uroot -proot -e 'select * from project_exam_system.user'
   
   
   
   # 有的时候不希望每次都设置密码和导入数据库，所以，我们会把数据库提前备份到data目录中，这样，直接挂载在容器上，就可以直接使用
   
   
   
   docker run -itd -p3306 -v /root/pes/msyql/data/:/var/lib/mysql/ mysql:5.7.44
   
   ```
   
   ```mysql
   # 数据的用户和权限
   select host,user from msyql.user;
   # 创建用户
   create user 'zm'@'%' identified by 'zm';
   # 添加权限
   grant all on *.* to 'zm'@'%';
   # 刷新权限
   flush privileges;
   
   
   # 允许root远程访问
   update mysql.user set host='%' where user='root';
   flush privileges;
   ```
   
3. 部署tomcat 的java环境

   ```bash
   # 查看tomcat镜像
   docker images
   # 启动测试
   docker run -itd -p8080 tomcat:latest
   # 查看java本刊
   docker exec -it 容器id java --version
   # 查看工作目录 
   dokcer exec -it 容器id /bin/bash
   # 以上指令默认的目录就是工作目录
   
   # 修改配置文件application.properties
   vi /root/pes/java/src/application.properteis
   
   
   docker run -itd -p8080:8080 -v /root/pes/java/src/:/usr/local/tomcat/ tomcat:latest java -jar Project_ExamSystem.war
   
   # 查看容器日志
   docker logs 容器id
   
   # 无法连接到数据库，需要使用link
   # 重新创建带name的mysql容器
   # 重新创建带link的tomcat容器
   docker run -idt --name mysql0 -v /root/pes/mysql/data/:v /var/lib/mysql/ mysql:5.7.44
   docker run -idt --link mysql0:db -v /root/pes/java/src/:/usr/local/tomcat/ -p8080:8080 tomcat:latest java -jar Project_ExamSystem.war
   ```
   
   # 回顾
   
   1.在创建容器的是这添加环境变量
   
   ```bash
   docker run -e a=3 -e b=4 centos:7
   -e 向容器的添加环境变量
   ```
   
   2.如果执行run执行的时候，没有找到指定镜像，会主动拉取镜像
   
   3.系统centos ubuntu这样的系统 一般会-it
   
   4.-d让容器在后台启动，同时输出容器id
   
   5.我们只想让容器执行一个指令然后退出
   
   ```bash
   docker run --rm python:latest python --verison
   # python --version这个指令在容器内瞬间完成，不会长时间占据容器的前台，如果前台没有运行进程，容器会进入exited状态，我们在run的选项 --rm大的效果是当容器进入exited状态后，自动rm容器
   ```
   
   6.进入到容器内部管理

```bash
docker attach 容器id或者容器名称
#一般不会选attach，一般容器前台都会被指定的程序占据。
# ubuntu和centos比较例外，因为默认前台运行的是bash
docker exec -it 容器id或者容器名称 需要在容器中执行的指令
# exec比较灵活，会在容器中新开进程
```

7.使用attach挂载前台，不可以使用exit退出进程，否则会导致容器exited

8.要让容器跟着宿主机重启 可以在run创建容器的时候 添加 --restart always



7部署java

1. 检查tomcat容器中java版本

   ```bash
   docker run --rm tomcat:latest java --version
   ```

   这个软件是17版本开发的，21已经合适，接下来只需要将项目部署到容器中可以了

   版本：一般来说，高版本会兼容底本版，高版本一定能做低版本的任务。

2. 第一次尝试部署java

   ```
   docker run -v /root/java/src/:/src -8080:8080 tomcat:latest
   ```

   在tomcat容器中，在初始化的时候，会默认启动tomcat服务 /user/local/tomcat/bin/startup.sh

   ```
   docker run -d -v /root/pes/java/src/:/src/ tomcat java -jar /src/Project_ExamSystem-v.0.1.war
   ```

   
