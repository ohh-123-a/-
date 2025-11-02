# Ubuntu 24.04 安装 Zabbix 详细步骤

## 部署ubuntu-ssh

```yaml
version: '3'
services:
  ubuntu:
    image: ubuntu-ssh:1.0
    container_name: ubuntu-ssh
    expose:
      - 21
      - 22
      - 80
      - 3306
      - 8080
    ports:
      - 21
      - 22
      - 80
      - 3306
      - 8080
    volumes:
      - /ubuntu/ubuntu/
    privileged: true
```



## 1. 介绍

Zabbix 是一个开源的监控解决方案，广泛用于监控网络、服务器、应用程序和云服务的性能和可用性。它支持多种监控方式，包括主动和被动监控，并提供丰富的告警和报告功能。

## 2. 应用领域

- **网络监控**：监控网络设备的性能和状态。
- **服务器监控**：监控服务器的 CPU、内存、磁盘等资源使用情况。
- **应用程序监控**：监控应用程序的性能和可用性。
- **云服务监控**：监控云服务的性能和可用性。

## 3. 源码和详细注释

### 安装步骤

1. **更新系统**

   ```bash
   sudo apt update
   sudo apt upgrade -y

2. **安装必要的依赖**

   ```bash
   sudo apt install -y apache2 mysql-server php libapache2-mod-php php-mysql php-gd php-bcmath php-mbstring php-xml php-ldap php-net-socket php-soap php-zip
   ```

3. **下载并安装 Zabbix**

   ```bash
   wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu24.04_all.deb
   sudo dpkg -i zabbix-release_6.0-1+ubuntu24.04_all.deb
   sudo apt update
   sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-agent
   ```

4. **配置 MySQL 数据库**

   ```bash
   sudo mysql -u root -p
   ```

   在 MySQL 中执行以下命令：

   ```sql
   CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
   CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
   GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

5. **导入 Zabbix 数据库**

   ```bash
   zcat /usr/share/doc/zabbix-server-mysql/create.sql.gz | mysql -u zabbix -p zabbix
   ```

6. **配置 Zabbix 服务器**

   编辑 `/etc/zabbix/zabbix_server.conf` 文件：

   ```bash
   sudo nano /etc/zabbix/zabbix_server.conf
   ```

   修改以下内容：

   ```ini
   DBHost=localhost
   DBName=zabbix
   DBUser=zabbix
   DBPassword=password
   ```

7. **启动 Zabbix 服务**

   ```bash
   sudo systemctl restart zabbix-server zabbix-agent apache2
   sudo systemctl enable zabbix-server zabbix-agent apache2
   ```

8. **访问 Zabbix Web 界面**

   打开浏览器，访问 `http://your-server-ip/zabbix`，按照向导完成安装。

## 4. 案例

### 案例 1: 监控服务器 CPU 使用率

#### 案例介绍

本案例演示如何使用 Zabbix 监控服务器的 CPU 使用率。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `system.cpu.util[,idle]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:system.cpu.util[,idle].last()}<20`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('Server1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (1, 'CPU Usage', 'system.cpu.util[,idle]', 0, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('High CPU Usage', '{Server1:system.cpu.util[,idle].last()}<20', 4);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到服务器的 CPU 使用率图表，并在 CPU 使用率超过 80% 时触发告警。

### 案例 2: 监控网络设备

#### 案例介绍

本案例演示如何使用 Zabbix 监控网络设备的性能和状态。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `net.if.in[eth0]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:net.if.in[eth0].last()}>1000000`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('Router1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (2, 'Network Traffic In', 'net.if.in[eth0]', 3, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('High Network Traffic', '{Router1:net.if.in[eth0].last()}>1000000', 4);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到网络设备的网络流量图表，并在流量超过 1Mbps 时触发告警。

### 案例 3: 监控应用程序性能

#### 案例介绍

本案例演示如何使用 Zabbix 监控应用程序的性能。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `web.test.time[MyApp,HomePage]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:web.test.time[MyApp,HomePage].last()}>5000`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('AppServer1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (3, 'App Response Time', 'web.test.time[MyApp,HomePage]', 0, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('High App Response Time', '{AppServer1:web.test.time[MyApp,HomePage].last()}>5000', 4);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到应用程序的响应时间图表，并在响应时间超过 5 秒时触发告警。

### 案例 4: 监控云服务

#### 案例介绍

本案例演示如何使用 Zabbix 监控云服务的性能和可用性。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `cloud.service.status[MyCloudService]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:cloud.service.status[MyCloudService].last()}=0`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('CloudService1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (4, 'Cloud Service Status', 'cloud.service.status[MyCloudService]', 3, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('Cloud Service Down', '{CloudService1:cloud.service.status[MyCloudService].last()}=0', 5);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到云服务的状态图表，并在服务不可用时触发告警。

### 案例 5: 监控数据库性能

#### 案例介绍

本案例演示如何使用 Zabbix 监控数据库的性能。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `db.connections[MySQL]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:db.connections[MySQL].last()}>100`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('DBServer1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (5, 'DB Connections', 'db.connections[MySQL]', 3, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('High DB Connections', '{DBServer1:db.connections[MySQL].last()}>100', 4);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到数据库的连接数图表，并在连接数超过 100 时触发告警。

### 案例 6: 监控磁盘空间

#### 案例介绍

本案例演示如何使用 Zabbix 监控磁盘空间。

#### 操作详细步骤

1. **登录 Zabbix Web 界面**
2. **创建主机**
   - 进入 `Configuration` -> `Hosts` -> `Create host`
   - 填写主机名称和 IP 地址
3. **添加监控项**
   - 进入 `Configuration` -> `Hosts` -> `Items` -> `Create item`
   - 填写监控项名称和键值 `vfs.fs.size[/,free]`
4. **创建触发器**
   - 进入 `Configuration` -> `Hosts` -> `Triggers` -> `Create trigger`
   - 设置触发器名称和表达式 `{HOSTNAME:vfs.fs.size[/,free].last()}<10485760`

#### 源码和注释

```sql
-- 创建主机
INSERT INTO hosts (host, status, available) VALUES ('StorageServer1', 0, 1);

-- 添加监控项
INSERT INTO items (hostid, name, key_, value_type, delay) VALUES (6, 'Free Disk Space', 'vfs.fs.size[/,free]', 3, 60);

-- 创建触发器
INSERT INTO triggers (description, expression, priority) VALUES ('Low Disk Space', '{StorageServer1:vfs.fs.size[/,free].last()}<10485760', 4);
```

#### 代码解释

- `hosts` 表用于存储主机信息。
- `items` 表用于存储监控项信息。
- `triggers` 表用于存储触发器信息。

#### 结果展示

在 Zabbix Web 界面中，可以看到磁盘空间的图表，并在磁盘空间不足 10GB 时触发告警。
```