# Docker 网络和自定义网络的创建与使用

## 1. 创建 Docker 网络

Docker 提供了多种网络模式，包括 `bridge`、`host`、`none` 等。默认情况下，Docker 会创建一个名为 `bridge` 的网络，容器会自动连接到这个网络。

### 1.1 创建自定义网络

你可以使用以下命令创建一个自定义网络：

```bash
docker network create --driver bridge my_custom_network
```

- `--driver bridge`：指定网络驱动为 `bridge`（默认驱动）。
- `my_custom_network`：自定义网络的名称。

### 1.2 查看网络列表

创建完成后，可以使用以下命令查看当前的 Docker 网络列表：

```bash
docker network ls
```

## 2. 使用自定义网络启动容器

### 2.1 启动容器并连接到自定义网络

使用以下命令启动一个容器并将其连接到自定义网络：

```bash
docker run -d --name my_container --network my_custom_network nginx
```

- `--name my_container`：指定容器的名称。
- `--network my_custom_network`：指定容器连接的网络。

### 2.2 查看容器网络信息

可以使用以下命令查看容器的网络信息：

```bash
docker inspect my_container
```

在输出中，你可以找到容器的 IP 地址、网关等信息。

## 3. 固定容器 IP 地址

在某些情况下，你可能希望为容器分配一个固定的 IP 地址。可以通过以下步骤实现：

### 3.1 创建自定义网络时指定子网

在创建自定义网络时，可以指定子网和 IP 地址范围：

```bash
docker network create --driver bridge --subnet 172.20.0.0/16 --gateway 172.20.0.1 my_custom_network
```

- `--subnet 172.20.0.0/16`：指定子网范围。
- `--gateway 172.20.0.1`：指定网关地址。

### 3.2 启动容器并指定 IP 地址

使用以下命令启动容器并为其分配固定的 IP 地址：

```bash
docker run -d --name my_container --network my_custom_network --ip 172.20.0.10 nginx
```

- `--ip 172.20.0.10`：指定容器的 IP 地址。

### 3.3 验证固定 IP 地址

使用以下命令查看容器的网络信息，确认 IP 地址是否已正确分配：

```bash
docker inspect my_container
```

在输出中，你应该能够看到容器的 IP 地址为 `172.20.0.10`。

## 4. 总结

通过创建自定义网络并指定子网和 IP 地址，你可以更好地管理 Docker 容器的网络配置，确保容器之间的通信更加稳定和可控。
