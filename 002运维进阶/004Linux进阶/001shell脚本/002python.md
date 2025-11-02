## 9月25日 (Python 脚本：远程执行)

**学习目标：** 学习使用 Python 在多个远程服务器上执行命令的基本方法，了解使用 `paramiko` 或系统 `ssh` 方式实现批量运维操作[blog.csdn.net](https://blog.csdn.net/pymzy666skr/article/details/140651537#:~:text=)。掌握捕获命令输出、异常处理等技能。
 **任务描述：** 针对 CDN 运维场景，编写一个 Python 脚本，自动化登录多台边缘节点执行指定命令（如获取系统状态、检查服务状态等），并返回执行结果。可以使用 `paramiko` 库（需安装）或直接调用系统的 `ssh` 命令。

- 第一步：准备远程主机列表（IP或域名）和要执行的命令，如 `uptime` 或自定义运维命令。
- 第二步：如果使用 `paramiko`，则通过 `SSHClient` 连接并 `exec_command` 执行命令[blog.csdn.net](https://blog.csdn.net/pymzy666skr/article/details/140651537#:~:text=client %3D paramiko.SSHClient() ,exec_command(command) 使用 exec_command)；如果使用系统 `ssh`，则通过 `subprocess.run()` 调用。例如：`subprocess.run(["ssh", host, cmd])`。
- 第三步：收集并打印各主机返回结果，处理可能的连接异常。

**参考脚本/命令：**

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
远程执行脚本示例：依次连接多台服务器并执行命令。
"""
import subprocess

servers = ["192.168.1.101", "192.168.1.102"]  # 远程主机IP列表
command = "uptime"

for host in servers:
    print(f"连接到 {host} 并执行命令：{command}")
    try:
        # 调用系统 ssh 执行远程命令，需预先配置无密码登录
        result = subprocess.run(["ssh", host, command], capture_output=True, text=True)
        output = result.stdout.strip()
        print(f"{host} 返回：\n{output}\n")
    except Exception as e:
        print(f"{host} 连接失败：{e}")
```

脚本中使用了 `subprocess.run` 调用系统 SSH 客户端执行远程命令（假设已配置免密登录）。如果使用 `paramiko` 库，则需要先执行 `pip install paramiko`，并按[blog.csdn.net](https://blog.csdn.net/pymzy666skr/article/details/140651537#:~:text=client %3D paramiko.SSHClient() ,exec_command(command) 使用 exec_command)所示创建 `SSHClient` 连接。无论哪种方式，核心是通过 SSH 安全地执行远程运维命令[blog.csdn.net](https://blog.csdn.net/pymzy666skr/article/details/140651537#:~:text=)。