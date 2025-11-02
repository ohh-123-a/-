1. 拉取registry:latest

   ```bash
   docker pull registry
   ```

   

2. 部署registry容器

   ```bash
   docker run -d --restary always -v /opt/dockerimageregistry/:/var/lib/registry/ -p50000:5000 registry:latest
   ```

   

3. 上传镜像

   1. 配置安全仓库

      ```json
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
                                      "https://docker.rainbond.cc"
              ],
              "insecure-registries":[
                      "http://10.1.1.3:50000"
              ]
      }
      ```

   2. 重启docker服务

      ```bash
      systemctl restart docker
      ```

      

   3. 设置标签

      ```bash
      docker tag alpine:latest 10.1.1.3:50000/alpine:latest
      ```

      

   4. 推送镜像

      ```bash
      docker push 10.1.1.3:50000/alpine:latest
      ```

      

4. 查看仓库镜像

   1. 查看镜像

      ```bash
      curl http://10.1.1.3:50000/v2/_catalog
      ```

      

   2. 查看镜像版本

      ```bash
      curl http://10.1.1.3:50000/v2/alpine/tags/list
      ```

      

5. 拉取镜像

   1. 配置安全仓库

      ```json
      		"insecure-registries":[
                      "http://10.1.1.3:50000"
              ]
      ```

   2. 重启docker服务

      ```bash
      systemctl restart docker
      ```

   3. 查看镜像和版本

      ```bash
      curl http://10.1.1.3:50000/v2/_catalog
      curl http://10.1.1.3:50000/v2/alpine/tags/list
      ```

   4. 拉取镜像

      ```bash
      docker pull 10.1.1.3:50000/alpine:latest
      ```

   
   总结：
   
   1. registey仓库是一个简单镜像，提供docker镜像存储和拉取功能
   
   2. 容器内保存镜像的位置/var/lib/registry/,为了持续保存镜像，我们在创建容器的时候，可以将该目录挂载/var/lib/registry/挂载宿主机，这样，即使容器损毁，镜像数据也不会丢失。
   
   3. 我们在部署仓库的时候，没有https权限，而docker的pull和push默认https权限的，所以，要让docker能够从registry容器上获取服务，就应该，在daemon.json中配置安全仓库
   
      ```json
      {
      	"insecure-registries":[
      		"http://10.1.1.3:50000"
      	]
      }
      // 重启docker服务以后，pull和push就可以直接访问该仓库
      ```
   
   4. 查看仓库中的镜像和版本
   
      ```bash
      curl http://<ip|域名>:端口/v2/_catalog   #查看惊醒
      curl http://<ip|域名>:端口/v2/镜像名/tags/list # 查看镜像版本
      ```
   
   5. 添加标签的目的是为了指向拉取镜像的仓库
   
      ```bash
      docker tag 原来的镜像:原来的版本号  <ip|域名>:<端口>/新的镜像名:新的版本号
      ```
   
   6. registry仓库的拉取和推送没有权限要求，不要登录，可以用于个人的镜像仓库。
   
      ```bash
      dockker push 10.1.1.3:50000/alpine:latest
      dockerr pull 10.1.1.3:50000/alpine:latest
      ```
   
      