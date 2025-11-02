docker配置《学生考试系统》

步骤

1. 创建目录，添加文件

   ```bash
   # 创建项目目录
   mkdir ~/docker-compose-pes-lb
   # 跳转到项目目录
   cd ~/docker-compose-pes-lb
   # 创建docker-compose的核心问价
   touch docker-compose.yml
   # 将haprxy配置文件的蓝本复制到当前目录
   cp ~/haporxy.cfg .
   
   # 检查pes项目文件
   ls -l ~/pes/
   ```

2. 编辑docker-compose.yml文件

   ```yaml
   # 指定版本
   version: '3'
   # 开始编写服务
   services:
   	# 服务名
   	web0:
   		# 镜像
   		image: nginx:latest
   		# 容器名
   		container_name: web0
   		# 挂载卷，由于可能挂载多个卷，所以是数组形式，- 也要空格
   		volumes:
   			- /root/pes/web/dist/:/usr/share/nginx/html/
   		# 向宿主机暴露端口，这个也是可以同时暴露多个端口的
   		expose:
   			- 80
   		# 设置容器自动重启
   		restart: always
   	java0:
   		image: tomcat:latest
   		container_name: java0
   		volumes:
   			# 将项目挂载到tomcat容器的工作目录 /usr/local/tomcat
   			- /root/pes/java/src/:/usr/local/tomcat/
   		expose:
   			- 8080
   		# 设置开机启动指令，替代容器默认的开机指令，这个指令必须是能够在前台运行，并且不会直接退出的。
   		command: java -jar Project_ExamSystem-V1.0.0.war
   		restart: alwarys
   	db:
   		image: mysql:5.7.44
   		container_name: db
   		volumes:
   			- /root/pes/mysql/data/:/var/lib/mysql/
   		expose:
   			- 3306
   		restart: always
   	haproxy:
   		image: haproxy:latest
   		container_name: haproxy
   		volumes:
   			- ./haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
   		export:
   			- 5000
   			- 5001
   			- 8888
   		ports:
   			- '80:5000'
   			- '32100:5001'
   			- '33333:8888'
   			# 要访问前端，就需要访问haproxy的5000端口，5000端口代理nginx的80端口
   			# 要求访问java后台，就需要访问haproxy的5001端口，5001端口代理tomcat的8080端口
   			# 要求访问数据看板，要访问haproxy容器的8888，这是直接访问的
   			# 以上的三个端口是haproxy对宿主机暴露的端口，要在外部访问，需要将端口映射出来。
   ```

3. haproxy的配置文件

   ```cfg
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
   frontend a
       bind *:5000
       default_backend b
   
   # 后端配置，定义服务器池
   backend b
       balance roundrobin
       server web0 web0:80 check
   
   frontend c
       bind *:5001
       default_backend d
   
   # 后端配置，定义服务器池
   backend d
       balance roundrobin
       server java0 java0:8080 check
   
   # 前端配置，用于 Dashboard
   frontend stats
       bind *:8888
       mode http
       stats enable
       stats uri /haproxy_stats
       stats refresh 5s
       stats auth admin:admin
       stats hide-version
       stats show-node
       stats auth admin:your_password
       stats admin if TRUE
   
   ```

   