## docker 图形化界面 安装详细步骤

以下为你详细介绍几种常见 Docker 图形化界面的安装步骤：

### Portainer

Portainer 是一款轻量级的开源 Docker 管理界面，支持多种安装方式，这里以 Docker 容器方式安装为例。

#### 环境准备

确保你已经安装了 Docker，并且 Docker 服务正在运行。可以通过以下命令验证：

```bash
docker --version
docker ps
```

#### 安装步骤

1. **创建 Portainer 数据卷**
   Portainer 需要一个数据卷来存储配置信息，使用以下命令创建：

```bash
docker volume create portainer_data
```

1. **运行 Portainer 容器**
   使用以下命令下载并运行 Portainer 容器：

```bash
docker run -d -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

- `-d`：表示在后台运行容器。
- `-p 9000:9000`：将容器的 9000 端口映射到宿主机的 9000 端口。
- `--name portainer`：为容器指定名称为 `portainer`。
- `--restart=always`：设置容器在 Docker 服务重启时自动启动。
- `-v /var/run/docker.sock:/var/run/docker.sock`：将 Docker 守护进程的套接字文件挂载到容器中，以便 Portainer 可以与 Docker 守护进程通信。
- `-v portainer_data:/data`：将之前创建的数据卷挂载到容器的 `/data` 目录。

1. **访问 Portainer**
   在浏览器中输入 `http://<服务器 IP 地址>:9000`，即可访问 Portainer 界面。首次访问时，需要设置管理员密码，并选择连接本地 Docker 环境。

### Docker Desktop

Docker Desktop 是 Docker 官方提供的图形化工具，支持 Windows 和 macOS 系统。

#### 安装步骤（以 Windows 为例）

1. **下载安装包**
   访问 Docker 官方网站（https://www.docker.com/products/docker-desktop），点击下载适用于 Windows 的 Docker Desktop 安装包。
2. **运行安装程序**
   下载完成后，双击安装包文件，按照安装向导的提示进行安装。安装过程中可能需要重启计算机。
3. **启动 Docker Desktop**
   安装完成后，在开始菜单中找到 Docker Desktop 并启动它。启动后，系统托盘中会出现 Docker 图标，表示 Docker 服务正在运行。

#### 安装步骤（以 macOS 为例）

1. **下载安装包**
   同样访问 Docker 官方网站，点击下载适用于 macOS 的 Docker Desktop 安装包。
2. **安装 Docker Desktop**
   下载完成后，将 Docker 图标拖放到 “应用程序” 文件夹中，然后在 “应用程序” 中找到 Docker Desktop 并打开。
3. **授权并启动**
   打开 Docker Desktop 时，系统会提示输入管理员密码进行授权。授权完成后，Docker 服务将自动启动。

### Rancher

Rancher 是一个开源的容器管理平台，支持 Docker 环境。

#### 环境准备

确保你已经安装了 Docker，并且 Docker 服务正在运行。

#### 安装步骤

1. **运行 Rancher 容器**
   使用以下命令下载并运行 Rancher 容器：

```bash
docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher
```





- `-d`：在后台运行容器。
- `--restart=unless-stopped`：设置容器在 Docker 服务重启时自动启动，除非手动停止。
- `-p 80:80 -p 443:443`：将容器的 80 和 443 端口映射到宿主机的 80 和 443 端口。

1. **访问 Rancher**
   在浏览器中输入 `https://<服务器 IP 地址>`，即可访问 Rancher 界面。首次访问时，需要设置管理员密码，并完成初始化设置。