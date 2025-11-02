使用docker部署了《学生考试系统》

```bash
# 部署web前端
# 将dist目录部署到nginx容器中，挂载目录，映射端口
docker run -d -v /root/pes/web/dist/:/usr/share/nginx/html/ -p 80:80 nginx:latest
# -d 让容器在后台运行，并且输出容器id
# -v 将目录磁盘挂载到容器中，冒号前是web项目文件，冒号后是容器中nginx服务的资源目录
# 部分同学习惯修改nginx.conf,可以在宿主机上修改完成后，挂载到-v /root/nginx.conf:/etc/nginx/nginx.conf -v /root/pes/web/dist/:/html/
# -p 80:80 将容器的80映射到宿主机的80端口，当远程主机访问宿主机器的80端口，会被转发到容器的80端口。
# -p80 这种方式表示将容器端口映射到宿主机的随机端口，这个端口端口大于32767
# 部署完成以后，在浏览器上访问web服务。
# 随意使用账号密码登录，查看网络，查看发送请求的地址，端口，响应的状态，响应的内容
# 我们要通过查看网络清楚知道接下来要部署的java容器需要提供什么养的端口和服务


# 部署数据库
docker run -d -v /root/pes/mysql/data/:/var/lib/mysql/ --name mysql0 mysql:5.7.44
# -v 将备份文件直接挂载到容器的mysql目录，不需要初始化，直接使用
# --name 指定容器的名称
# 没有将mysql的端口映射，也就是说在容器外部，远程主机无法访问数据
# 如果希望拿到数据库的数据，就要求访问java来获取

# 部署java服务
# 需要一个有jdk17以上的java环境的容器，tomcat:latest,maven:latest
# 也可以自己创建带有高版本的镜像 Dockerfile
# 如果自己创建，可能镜像会超过700M，我们这边是直接使用tomcat容器

# tomcat容器需要连接mysql容器，但是容器的ip地址不固定，所以为了保障总是能够连接mysql容器，使用link选项 连接mysql0. --link mysql0:db,为什么是db，因为java项目的application.properrrtes文件中指定了访问数据库的域名就是db
# 我们在部署java服务之间，应该先查看或者修改application.properties文件
# --link mysql0:db 会在tomcat容器的host中添加一个db域名指向mysql容器
# 由于tomcat容器有默认的开机前台指令，我们希望tomcat容器在开机的时候自动执行我们挂载java项目，所有run 容器后，要添加前台指令
# 要启动我们的java项目，需要在项目目录下运行 java -jar，否则，java项目会无法读取到配置文件。
# 我们会将java的项目文件挂载到tomcat的工作目录，经过测试，tomcat的工作目录。/usr/local/tomcat
# 为前端提供服务，要查看前端请求的端口，所以tomcat容器映射的端口是多少
docker run -d -v /root/pes/java/src/:/usr/local/tomcat/ --link mysql0:db -p 32100:8080 tomcat java -jar Project_ExamSystem-V1.0.0.war
# -v 将java项目目录挂载到容器的工作目录
# -p 将自己8080端口映射到32100，因为前端访问宿主机也是访问32100
# --link mysql0:db 通过mysql0这个名称指向mysql容器，在tomcat容器中的hosts文件，添加mysql的域名为db
# java -jar **.war 是在容器启动的时候自动执行的指令，他替代了tomcat默认的前台指令

# 在项目部署过程，我们需要经常查看容器日志
# 在前端访问java服务的时候，检查tomcat容器的日志
docker logs 容器id
# 一般会有三种情况
# 1.tomcat容器 没哟正常启动，用户服务访问
# 2.tomcat容器正常启动，但是用户访问的端口错误
# 3.tomcat容器正常启动，但是无法正常连接数据库
```



# HAProxy 指南![image-20250317090510672](./assets/image-20250317090510672.png)

## 1. HAProxy 简介

docker pull haproxy

HAProxy 是一个高性能的 TCP/HTTP 负载均衡器和代理服务器，广泛用于高可用性和负载均衡的场景。它支持多种负载均衡算法，并且能够处理大量的并发连接。

高可用

1.什么是高可用

2.如何实现高可用

## 2. HAProxy 的详细原理
HAProxy 通过监听客户端请求并将其转发到后端服务器来实现负载均衡。它支持多种负载均衡算法，如轮询（Round Robin）、最少连接（Least Connections）等。HAProxy 还提供了健康检查功能，确保流量只被转发到健康的服务器。

## 3. HAProxy 安装
在大多数 Linux 发行版上，可以通过包管理器安装 HAProxy。例如，在 Ubuntu 上可以使用以下命令：

```bash
sudo apt-get update
sudo apt-get install haproxy

## 4. HAProxy 配置文件
HAProxy 的配置文件通常位于 `/etc/haproxy/haproxy.cfg`。配置文件定义了前端（Frontend）和后端（Backend）的配置，以及负载均衡策略、健康检查等。

```haproxy
global
    log /dev/log    local0
    log /dev/log    local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http_front
    bind *:80
    default_backend http_back

backend http_back
    balance roundrobin
    server web1 192.168.1.101:80 check
    server web2 192.168.1.102:80 check
```

## 5. HAProxy 的 Dashboard
HAProxy 提供了一个内置的统计页面，可以通过配置启用。在配置文件中添加以下内容以启用统计页面：

```haproxy
listen stats
    bind *:8080
    stats enable
    stats uri /stats
    stats auth admin:password
```

访问 `http://your-server-ip:8080/stats` 并使用配置的用户名和密码登录即可查看 HAProxy 的统计信息。

## 6. HAProxy 使用
HAProxy 可以通过命令行工具进行管理。常用的命令包括：

- 启动 HAProxy：`sudo systemctl start haproxy`
- 停止 HAProxy：`sudo systemctl stop haproxy`
- 重启 HAProxy：`sudo systemctl restart haproxy`
- 查看状态：`sudo systemctl status haproxy`

## 7. HAProxy Docker 容器
HAProxy 也可以作为 Docker 容器运行。可以使用以下命令拉取并运行 HAProxy 容器：

```bash
docker run -d --name haproxy -p 80:80 -p 8080:8080 haproxy:latest
```

## 8. HAProxy 容器部署
在 Docker 容器中部署 HAProxy 时，可以通过挂载配置文件或使用环境变量来配置 HAProxy。例如：

```bash
docker run -d --name haproxy -p 80:80 -p 8080:8080 -v /path/to/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg haproxy:latest
```

## 9. HAProxy 案例1
**场景**：负载均衡两个 Web 服务器。

**配置**：
```haproxy
frontend http_front
    bind *:80
    default_backend http_back

backend http_back
    balance roundrobin
    server web1 192.168.1.101:80 check
    server web2 192.168.1.102:80 check
```

## 10. HAProxy 案例2
**场景**：使用 HAProxy 进行 SSL 终止。

**配置**：
```haproxy
frontend https_front
    bind *:443 ssl crt /etc/haproxy/certs/example.com.pem
    default_backend http_back

backend http_back
    balance roundrobin
    server web1 192.168.1.101:80 check
    server web2 192.168.1.102:80 check
```

---

以上是 HAProxy 的基本使用指南，涵盖了从安装到配置、使用和部署的各个方面。希望对你有所帮助！

实验：

1. 创建haproxy.cfg文件，修改前端端口，修改代理的端口和主机,当用户访问haproxy容器的5000端口的时候，会轮询 172.17.0.2-4的80端口

   ```
   # 全局设置
   global
       user haproxy
       group haproxy
       maxconn 256000
       log /dev/log local0
       log /dev/log local1 notice
       daemon
   
   # 默认设置
   defaults
       log     global
       mode    http
       timeout connect 5000ms
       timeout client  50000ms
       timeout server  50000ms
   
   # 前端配置，处理进入的 HTTP 请求
   frontend http-in
       bind *:5000
       default_backend servers
   
   # 后端配置，定义服务器池
   backend servers
       balance roundrobin
       server server1 172.17.0.2:80 check
       server server2 172.17.0.3:80 check
       server server3 172.17.0.4:80 check
   
   # 前端配置，用于 Dashboard
   frontend stats
       bind *:8404
       mode http
       stats enable
       stats uri /haproxy_stats
       stats refresh 5s
       stats auth admin:password
       stats hide-version
       stats show-node
       stats auth admin:your_password
       stats admin if TRUE
   ```

2. 启动容器 代理3个web服务

   ```bash
   dcoker run -d -v /root/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg haproxy:latest
   ```

3. 启动完成以后，的确可以在宿主机上使用curl访问haproxy的5000端口，来轮训三个nginx服务

4. 创建新的haproxy容器，将haproxy 5000端口映射到宿主机的80端口，这样，外部远程主机可以用80端口访访问haproxy的5000端口，而且，内部的三个nginx主机无法被外部访问

   ```
   docker run -d -p80:5000 -v /root/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg haproxy:latest
   ```

5. 由于ip地址是随时发生改变，所以，我们的配置文件不能直接写容器的id，那么我们就要想把法将ngixn的ip变成域名![image-20250317104308722](./assets/image-20250317104308722.png)

   ```bash
   docker run -d --link web0:web0 --link web1:web1 --link web2:web2 -p80:5000 -v /root/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg haproxy:latest
   ```

   

![image-20250317102800856](./assets/image-20250317102800856.png)

```bash
docker run -d --name web0 -v /root/pes/web/dist/:/usr/share/nginx/html/ nginx:latest

docker run -d --name webbalance -v /root/pes_web_blance.cfg:/usr/local/etc/haproxy/haproxy.cfg -p80:5000 --link web0 haproxy:latest


docker run -d --name mysql0 -v /root/pes/mysql/data/:/var/lib/mysql/ mysql:5.7.44


docker run -d -v /root/pes/java/src/:/usr/local/tomcat/ --name java0 --link mysql0:db tomcat:latest java -jar Project_ExamSystem-V1.0.0.war

docker run -d -v /root/pes/java/src/:/usr/local/tomcat/ --name java1 --link mysql0:db tomcat:latest java -jar Project_ExamSystem-V1.0.0.war

docker run -d -v /root/pes/java/src/:/usr/local/tomcat/ --name java2 --link mysql0:db tomcat:latest java -jar Project_ExamSystem-V1.0.0.war

docker run -d -v /root/pes_java_balance.cfg:/usr/local/etc/haproxy/haproxy.cfg -p32100:5000 --link java0 --link java1 --link java2:hava2 haproxy:latest 
```



## 案例

1. 容器 nginx haproxy

   ```bash
   docker pull haproxy
   #是在pull不行
   docker load -i haproxy.tar
   docker images
   ```

2. 创建三个nginx容器，分别命名为web0 web1 web2 ，修改容器中的index.html文件，让页面显示为web0 web1 web2,不需要向外暴露端口

   ```bash
   docker run -d --name web0 nginx:latest
   docker run -d --name web1 nginx:latest
   docker run -d --name web2 nginx:latest
   # 获取三个容器的ip
   docker inspect web0|grep IPA # 172.17.0.2
   docker inspect web1|grep IPA # 172.17.0.3
   docker inspect web2|grep IPA # 172.17.0.4
   
   curl 172.17.0.2:80
   curl 172.17.0.3:80
   curl 172.17.0.4:80
   
   # 进入容器修改三个页面上显示的内容
   echo '<h1>web0</h1>' > index.html
   docker cp index.html web0:/usr/share/nginx/html/
   
   echo '<h1>web1</h1>' > index.html
   docker cp index.html web1:/usr/share/nginx/html/
   
   echo '<h1>web2</h1>' > index.html
   docker cp index.html web2:/usr/share/nginx/html/
   
   curl 172.17.0.2:80
   curl 172.17.0.3:80
   curl 172.17.0.4:80
   ```

   ![image-20250318084750484](./assets/image-20250318084750484.png)

   

3. 创建web_blance.cfg

   ```
   # 全局设置
   global
       user haproxy
       group haproxy
       maxconn 256000
       log /dev/log local0
       log /dev/log local1 notice
       daemon
   
   # 默认设置
   defaults
       log     global
       mode    http
       timeout connect 5000ms
       timeout client  50000ms
       timeout server  50000ms
   
   # 前端配置，处理进入的 HTTP 请求
   frontend http-in
       bind *:5000
       default_backend servers
   
   # 后端配置，定义服务器池
   backend servers
       balance roundrobin
       server web0 172.17.0.2:80 check
       server web1 172.17.0.3:80 check
       server web2 172.17.0.4:80 check
   
   # 前端配置，用于 Dashboard
   frontend stats
       bind *:8404
       mode http
       stats enable
       stats uri /haproxy_stats
       stats refresh 5s
       stats auth admin:password
       stats hide-version
       stats show-node
       stats auth admin:your_password
       stats admin if TRUE
   ```
   
   ![image-20250318093102376](./assets/image-20250318093102376.png)
