

---下载Docker依赖的环境

```bash
yum -y install yum-utils device-mapper-persistent-data lvm2
```

---指定Docker镜像源

```bash
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

---安装Docker

```bahs
yum makecache fast
yum -y install docker-ce 
```

---启动Docker

```bash
systemctl start docker
```

 ---设置开机自动启动

```
systemctl enable docker
```

---配置docker镜像源

```
vi /etc/docker/daemon.json
------------------

{
  "registry-mirrors": [
    "https://docker.registry.cyou",
    "https://docker-cf.registry.cyou",
    "https://dockercf.jsdelivr.fyi",
    "https://docker.jsdelivr.fyi",
    "https://dockertest.jsdelivr.fyi",
    "https://mirror.aliyuncs.com",
    "https://dockerproxy.com",
    "https://mirror.baidubce.com",
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://docker.mirrors.sjtug.sjtu.edu.cn",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.iscas.ac.cn",
    "https://docker.rainbond.cc",
    "https://asj2dna1.mirror.aliyuncs.com"
  ],
  "insecure-registries": [
    "111.230.45.202:80"
  ]
}
----------------
```

---重置配置和docker服务

```
systemctl daemon-reload 
systemctl restart docker
```

---测试

```
docker run hello-world
```

---拉取镜像

```
docker pull 镜像名称[:tag 版本] （远程仓库的地址或者本地仓库的地址）
docker pull tomcat:9.0
```

---查看本地全部镜像

```
docker images
```

---删除本地镜像

```
docker rmi(remove  image) 镜像的标识（images id）
```

---运行容器

```
docker run -d -p 2222:22 python-ssh:latest
```

```bash
-d：代表后台运行容器
```

```
-p 宿主机端口:容器端口：为了映射当前Linux的端口和容器的端口
```

```
--name 容器名称：指定容器的名称
```

---查看正在运行的容器

```
docker ps [-qa]
```

```
-a：查看全部的容器，包括没有运行
```

```
-q：只查看容器的标识
```

---查看容器日志

```
docker logs -f 容器id
```

```
-f：可以滚动查看日志的最后几行
```

---进入容器内容部

```
docker exec -it 容器id bash
```

```
退出容器：exit
```

---挂载

```
docker run -itd -p2222:22 -v /pythonfilea:/pythonfileb  python-ssh:latest
```

---导入导出镜像

```
--导入
docker save mysql:latest -o /opt/mysql_image.tar
```

```
--导出
docker load -i mysql_image.tar
```

