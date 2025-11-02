# docker 定制ansible镜像

下面为你详细介绍如何定制一个包含 Ansible 的 Docker 镜像，步骤主要包括创建 Dockerfile、构建镜像、测试镜像等。

### 步骤 1：创建工作目录

首先，创建一个用于存放 Dockerfile 及相关文件的工作目录：

```bash
mkdir ansible-docker
cd ansible-docker
```

### 步骤 2：编写 Dockerfile

在 `ansible-docker` 目录下创建一个名为 `Dockerfile` 的文件，内容如下：

```Dockerfile
# 使用 CentOS 基础镜像
FROM centos:7

# 设置环境变量，避免语言环境警告
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8

# 更新系统并安装必要的软件包
RUN curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
RUN yum clean all && yum makecache

RUN yum -y install epel-release
RUN yum -y install  openssh-server openssh-clients sudo

# 安装 Ansible
RUN yum -y install ansible

# 生成 SSH 密钥
RUN ssh-keygen -A

# 创建 SSHD 运行所需的目录
RUN mkdir /var/run/sshd

# 设置 root 用户密码（可根据需要修改）
RUN echo 'root' |passwd --stdin root

# 允许 root 用户通过 SSH 登录
RUN sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

# 暴露 SSH 端口
EXPOSE 22

# 启动 SSH 服务
CMD ["/usr/sbin/sshd", "-D"]
```

### 步骤 3：准备 Ansible 配置文件和剧本

- **`ansible.cfg`**：在 `ansible-docker` 目录下创建 `ansible.cfg` 文件，用于配置 Ansible 的行为，示例内容如下：

```ini
[defaults]
inventory = /app/playbooks/inventory
remote_user = root
host_key_checking = False
```

- **`playbooks` 目录**：在 `ansible-docker` 目录下创建 `playbooks` 目录，并在其中添加你的 Ansible 剧本和清单文件。例如，创建一个简单的清单文件 `inventory`：

```plaintext
[webservers]
192.168.1.100
```

以及一个简单的剧本文件 `test.yml`：

```yaml
---
- name: Test Ansible
  hosts: webservers
  tasks:
    - name: Ping hosts
      ping:
```

### 步骤 4：构建 Docker 镜像

在 `ansible-docker` 目录下执行以下命令来构建包含 Ansible 的 Docker 镜像：

```bash
docker build -t ansible-custom:1.0 .
```

- `-t`：用于为镜像指定标签，格式为 `名称:版本`，这里的镜像名称是 `ansible-custom`，版本是 `1.0`。
- `.`：表示使用当前目录作为构建上下文。

### 步骤 5：测试 Docker 镜像

构建完成后，可以通过以下命令启动一个基于该镜像的容器，并在容器内执行 Ansible 命令进行测试：

```bash
docker run -it --rm ansible-custom:1.0 ansible all -m ping
```





- `-it`：以交互式模式运行容器，并分配一个伪终端。
- `--rm`：容器停止后自动删除。
- `ansible all -m ping`：在容器内执行 Ansible 的 `ping` 模块来测试与目标主机的连接。

### 注意事项

- 确保你的目标主机允许 SSH 连接，并且在 `ansible.cfg` 和 `inventory` 文件中正确配置了主机信息和 SSH 用户。
- 如果需要在容器内使用 SSH 密钥进行认证，可以将密钥文件复制到容器中，并在 `ansible.cfg` 中配置相应的密钥路径。例如，在 `Dockerfile` 中添加以下内容：

```Dockerfile
COPY id_rsa /root/.ssh/id_rsa
RUN chmod 600 /root/.ssh/id_rsa
```





通过以上步骤，你就可以定制一个包含 Ansible 的 Docker 镜像，并在容器内使用 Ansible 来管理远程主机。