## Ubuntu 20.04.6 LTS 国内安装 docker

在国内的 Ubuntu 20.04.6 LTS 系统上安装 Docker，为了提升下载速度，可使用国内镜像源。以下是详细的安装步骤：

### 1. 更新系统软件包列表

打开终端，运行以下命令来更新系统软件包列表，确保获取到最新的软件包信息：

```bash
sudo apt update
```

### 2. 安装必要的依赖包

执行以下命令安装允许 `apt` 使用 HTTPS 源的软件包，这些依赖能保证系统安全地从 HTTPS 源下载软件：

```bash
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

### 3. 添加 Docker 官方 GPG 密钥

添加 Docker 官方的 GPG 密钥，用于验证下载的 Docker 软件包的完整性和真实性：

```bash
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

### 4. 添加 Docker 软件源

将 Docker 的国内镜像软件源添加到系统的软件源列表中，以加快下载速度。这里以阿里云镜像源为例：

```bash
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

### 5. 再次更新软件包列表

添加新的软件源后，再次更新软件包列表，让系统识别 Docker 软件包：

```bash
sudo apt update
```

### 6. 安装 Docker 引擎

运行以下命令安装 Docker 引擎、Docker CLI 和 containerd：

```bash
sudo apt install docker-ce docker-ce-cli containerd.io
```

安装过程中，系统会提示确认安装，输入 `Y` 并回车继续。

### 7. 验证 Docker 安装

安装完成后，使用以下命令验证 Docker 是否安装成功，该命令会尝试运行一个名为 `hello-world` 的测试容器：

```bash
sudo docker run hello-world
```

若看到一系列输出信息，最后显示 `Hello from Docker!` 等内容，则说明 Docker 安装成功。

### 8. 配置 Docker 以非 root 用户运行（可选但推荐）

默认运行 Docker 命令需使用 `sudo` 权限，为避免每次输入 `sudo`，可将当前用户添加到 `docker` 用户组中：

```bash
sudo usermod -aG docker $USER
```

执行上述命令后，需注销并重新登录系统使更改生效。之后就可直接使用 `docker` 命令，无需 `sudo` 前缀。

### 9. 配置 Docker 镜像加速器（可选）

为进一步提升 Docker 镜像的下载速度，可配置 Docker 镜像加速器。以阿里云镜像加速器为例，编辑 `/etc/docker/daemon.json` 文件：

```bash
sudo nano /etc/docker/daemon.json
```

若文件不存在则会新建。在文件中添加以下内容：

```json
{
    "registry-mirrors": ["https://<你的阿里云加速器地址>.mirror.aliyuncs.com"]
}
```

将 `<你的阿里云加速器地址>` 替换为你自己在阿里云获取的加速器地址。

保存并退出文件（按下 `Ctrl + X`，然后按 `Y` 确认保存，最后按 `Enter` 退出 `nano` 编辑器）。

重启 Docker 服务使配置生效：

```bash
sudo systemctl restart docker
```

### 10. 启动并设置 Docker 开机自启

使用以下命令启动 Docker 服务，并设置 Docker 服务在系统开机时自动启动：

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

