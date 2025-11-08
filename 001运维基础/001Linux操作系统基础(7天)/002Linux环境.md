# ⚙️ 002 Linux环境

------

## 1️⃣ Shell的基本概念

🧠 **什么是 Shell：**
 Shell 是用户与 Linux 内核之间的“翻译层”，负责解释命令。

🔹 **常见 Shell 类型：**

| Shell 类型 | 命令路径        | 特点                 |
| ---------- | --------------- | -------------------- |
| Bash       | `/bin/bash`     | 默认，稳定且功能强大 |
| Zsh        | `/bin/zsh`      | 智能补全与美观提示   |
| Fish       | `/usr/bin/fish` | 配置简单，自动提示   |

💡 **查看系统支持的 Shell：**

```bash
cat /etc/shells
```

🧩 **命令行提示符格式：**

```bash
user@hostname:~$
```

------

## 2️⃣ 环境变量

📘 **环境变量作用：**
 存储系统运行时信息，影响命令查找、语言环境等。

🔹 **常见环境变量：**

| 变量名 | 含义               | 示例                           |
| ------ | ------------------ | ------------------------------ |
| `PATH` | 可执行文件搜索路径 | `/usr/local/bin:/usr/bin:/bin` |
| `HOME` | 当前用户主目录     | `/home/user`                   |
| `USER` | 当前用户名         | `user`                         |
| `LANG` | 系统语言设置       | `zh_CN.UTF-8`                  |

📘 **查看与设置变量：**

```bash
echo $PATH
export PATH=$PATH:/opt/bin
```

🧠 **让修改永久生效：**
 将变量写入 `~/.bashrc` 或 `~/.zshrc`，然后执行：

```bash
source ~/.bashrc
```

------

## 3️⃣ 配置文件

🗂️ **系统级配置文件（全局生效）**

- `/etc/passwd`：用户账户信息
- `/etc/hostname`：主机名
- `/etc/profile`：全局环境变量

👤 **用户级配置文件（仅当前用户生效）**

- `~/.bashrc`：交互式 Shell 启动时加载
- `~/.profile`：登录 Shell 时加载
- `~/.bash_history`：记录命令历史

📘 **刷新配置文件：**

```bahs
source ~/.bashrc
```

------

### ✅ 本节总结

- Shell 是命令交互界面。
- 环境变量控制程序行为。
- 配置文件分系统级与用户级。
