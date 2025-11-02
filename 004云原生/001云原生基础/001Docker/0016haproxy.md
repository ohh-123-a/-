## haproxy 3.0.4 配置文件 包含 dashboard

下面是一个包含 HAProxy Dashboard 配置的 HAProxy 3.0.4 配置文件示例，它能让你对 HAProxy 的运行状态进行监控：

```plaintext
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
    bind *:80
    default_backend servers

# 后端配置，定义服务器池
backend servers
    balance roundrobin
    server server1 192.168.1.100:80 check
    server server2 192.168.1.101:80 check
    server server3 192.168.1.102:80 check

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

### 配置解释

#### 全局设置（`global`）

- `user` 和 `group`：指定 HAProxy 运行的用户和组，提高安全性。
- `maxconn`：设定 HAProxy 允许的最大并发连接数。
- `log`：定义日志记录的目标和级别。
- `daemon`：使 HAProxy 以守护进程模式运行。

#### 默认设置（`defaults`）

- `log`：使用全局的日志设置。
- `mode`：指定 HAProxy 工作的模式为 `http`。
- `timeout`：分别设置连接超时、客户端超时和服务器超时时间。

#### 前端配置（`frontend`）

- **`http-in`**：监听所有 IP 地址的 80 端口，将请求转发到 `servers` 后端。

- `stats`

  ：

  - `bind *:8404`：监听所有 IP 地址的 8404 端口。
  - `stats enable`：启用统计信息展示。
  - `stats uri /haproxy_stats`：设置 Dashboard 的访问路径为 `/haproxy_stats`。
  - `stats refresh 5s`：设置统计信息的刷新间隔为 5 秒。
  - `stats auth admin:your_password`：设置访问 Dashboard 的认证信息，这里用户名是 `admin`，密码是 `your_password`，你需要将其替换为实际的密码。
  - `stats hide-version`：隐藏 HAProxy 的版本信息。
  - `stats show-node`：显示节点信息。
  - `stats admin if TRUE`：允许通过 Dashboard 对 HAProxy 进行管理操作。

#### 后端配置（`backend`）

- `balance roundrobin`：使用轮询算法进行负载均衡。
- `server`：列出后端服务器的信息，`check` 表示开启健康检查。

### 使用步骤

1. **保存配置**：把上述配置保存为 `haproxy.cfg` 文件。
2. **验证配置**：使用以下命令验证配置文件的语法是否正确：

```bash
haproxy -c -f /path/to/haproxy.cfg
```

1. **启动 HAProxy**：若配置验证通过，使用以下命令启动 HAProxy：

```bash
haproxy -f /path/to/haproxy.cfg
```

1. **访问 Dashboard**：在浏览器中访问 `http://your_server_ip:8404/haproxy_stats`，输入用户名和密码后即可查看 HAProxy 的运行状态。

### 注意事项

- 要把 `192.168.1.x` 替换为实际的后端服务器 IP 地址。
- 要把 `your_password` 替换为实际的认证密码。

config : parsing [/usr/local/etc/haproxy/haproxy.cfg:39]: Missing LF on last line, file might have been truncated at position 33

这个错误提示 `Missing LF on last line, file might have been truncated at position 33` 表明在配置文件的最后一行缺少换行符（LF，即 `\n`），并且文件可能在第 33 个字符处被截断了。以下是解决该问题的步骤：

### 1. 检查文件完整性

首先要确保 `haproxy.cfg` 文件没有损坏或被截断。你可以使用文本编辑器打开文件，查看文件内容是否完整，特别是文件末尾是否正常。

### 2. 添加换行符

在文件的最后一行添加一个换行符。不同的文本编辑器添加换行符的方式略有不同，以下是几种常见编辑器的操作方法：

#### 使用 Vim 编辑器

```bash
vim /usr/local/etc/haproxy/haproxy.cfg
```

进入 Vim 编辑器后，按 `G` 键跳转到文件末尾，再按 `o` 键进入插入模式，然后按下 `Enter` 键添加一个换行符，最后按 `Esc` 键退出插入模式，输入 `:wq` 保存并退出。

#### 使用 Nano 编辑器

```bash
nano /usr/local/etc/haproxy/haproxy.cfg
```

在 Nano 编辑器中，将光标移动到文件末尾，按下 `Enter` 键添加一个换行符，然后按 `Ctrl + X`，接着按 `Y` 确认保存，最后按 `Enter` 键退出。

### 3. 再次验证配置文件

添加换行符后，再次使用 `haproxy -c -f` 命令验证配置文件的语法：

```bash
haproxy -c -f /usr/local/etc/haproxy/haproxy.cfg
```

如果配置文件语法正确，你应该会看到类似如下的输出：

```plaintext
Configuration file is valid
```

### 4. 重新启动 HAProxy

如果配置文件验证通过，你可以重新启动 HAProxy 使新配置生效：

```bash
systemctl restart haproxy
```