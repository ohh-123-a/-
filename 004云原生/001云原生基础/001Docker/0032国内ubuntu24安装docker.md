国内 ubunt 24 安装docker

在国内的 Ubuntu 24 系统上安装 Docker，为了提升下载速度，你可以使用国内的镜像源。以下是具体的安装步骤：

### 1. 更新系统包索引

打开终端，执行以下命令更新系统的包索引：

```bash
sudo apt update
```

### 2. 安装必要的依赖包

这些依赖包能让 `apt` 通过 HTTPS 使用仓库：

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

### 3. 添加 Docker 官方的 GPG 密钥

```bash
curl -fsSL https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 4. 添加 Docker 软件源

使用清华大学的镜像源，执行以下命令添加 Docker 软件源：

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5. 再次更新包索引

```bash
sudo apt update
```

### 6. 安装 Docker 引擎

```bash
sudo apt install docker-ce docker-ce-cli containerd.io
```

### 7. 验证安装

安装完成后，通过以下命令验证 Docker 是否安装成功：

```bash
sudo docker run hello-world
```

如果成功安装，会下载并运行一个测试容器，输出信息表明 Docker 运行正常。

### 8. （可选）将用户添加到 `docker` 组

为了无需每次使用 `docker` 命令时都输入 `sudo`，可将当前用户添加到 `docker` 组：

```bash
sudo usermod -aG docker $USER
```

执行此命令后，需注销并重新登录，新的用户组设置才会生效。

### 9. 配置 Docker 镜像加速

为了进一步提升 Docker 镜像的下载速度，可以配置国内的 Docker 镜像加速器，例如阿里云、网易云等。以阿里云为例，配置步骤如下：

1. 登录阿里云控制台，在搜索框中输入 “容器镜像服务”，进入该服务页面。
2. 在左侧导航栏中选择 “镜像加速器”，会看到一个专属的加速器地址。
3. 创建或编辑 `/etc/docker/daemon.json` 文件，添加以下内容：

```json
{
    "registry-mirrors": ["https://<your-mirror-address>"]
}
```

将 `<your-mirror-address>` 替换为你在阿里云获取的加速器地址。

4. 重启 Docker 服务：

```bash
sudo systemctl restart docker
```

