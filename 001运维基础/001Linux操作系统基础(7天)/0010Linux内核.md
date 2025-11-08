# 🧠 010 Linux 内核管理

------

## 1️⃣ 🧩 什么是内核（Kernel）

**内核（Kernel）** 是 Linux 系统的灵魂，
 它运行在系统的最底层，直接与硬件交互。

主要职责包括：

| 功能           | 描述                         |
| -------------- | ---------------------------- |
| 🧮 进程管理     | 负责进程的创建、调度与销毁   |
| 🧠 内存管理     | 控制物理与虚拟内存           |
| 💾 文件系统     | 统一管理文件与存储设备       |
| 🔌 驱动管理     | 让操作系统能识别硬件设备     |
| 🌐 网络栈       | 提供 TCP/IP 网络通信         |
| 🔐 系统调用接口 | 提供用户态访问内核功能的入口 |

查看当前内核版本：

```bash
uname -a
uname -r
cat /proc/version
```

输出示例：

```bash
Linux ubuntu 6.2.0-39-generic #40~22.04.1-Ubuntu SMP x86_64 GNU/Linux
```

------

## 2️⃣ ⚙️ 内核结构组成

Linux 内核是一个 **模块化单内核（Monolithic Kernel）**：

```json
用户空间
 ├── Shell / 应用程序
 └── 系统调用接口（glibc）
内核空间
 ├── 系统调用处理
 ├── 进程调度器
 ├── 内存管理
 ├── 文件系统
 ├── 网络协议栈
 └── 驱动模块
硬件层
```

💡 **理解要点**：

- “单内核” 意味着所有核心功能都在同一个地址空间中；
- “模块化” 意味着可以动态加载/卸载驱动模块，而不重启系统。

------

## 3️⃣ 🧩 内核模块（Kernel Module）

Linux 内核支持“即插即用”，即 **模块化驱动**。

### 常见命令：

```bash
lsmod                     # 查看当前已加载模块
modinfo e1000e            # 查看模块信息（如驱动版本）
sudo modprobe module_name # 加载模块
sudo rmmod module_name    # 卸载模块
```

> 💡 `modprobe` 比 `insmod` 更智能，会自动加载依赖模块。

查看内核日志信息：

```bash
dmesg | tail
```

------

### 模块加载路径：

```bash
/lib/modules/$(uname -r)/
```

示例：

```bash
/lib/modules/6.2.0-39-generic/kernel/drivers/net/e1000e/e1000e.ko
```

`.ko`（Kernel Object） 文件就是可加载的内核模块。

------

## 4️⃣ 🧱 内核配置文件

内核有几个非常关键的配置文件路径👇：

| 文件路径                               | 作用                                      |
| -------------------------------------- | ----------------------------------------- |
| `/boot/config-$(uname -r)`             | 当前系统内核的配置项（编译参数）          |
| `/proc/config.gz`                      | 运行中内核的配置（需开启CONFIG_IKCONFIG） |
| `/etc/modprobe.d/*.conf`               | 模块加载配置文件                          |
| `/lib/modules/$(uname -r)/modules.dep` | 模块依赖关系                              |

📘 查看配置：

```bash
zcat /proc/config.gz | grep CONFIG_EXT4
```

📘 禁止某模块自动加载：

```bash
echo "blacklist nouveau" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
```

------

## 5️⃣ 🧰 内核编译流程

当需要修改内核功能或添加特定驱动时，可以自己编译内核。

### 编译步骤：

```bash
tar -xvf linux-*.tar.xz
cd linux-*

make menuconfig       # 打开内核功能配置界面（图形TUI）
make -j$(nproc)       # 编译内核与模块
sudo make modules_install install
sudo update-grub      # 更新引导器
sudo reboot
```

查看已安装的内核：

```bash
ls /boot/vmlinuz-*
```

> ⚙️ 建议：新手不必完全手动编译，可用 `apt install linux-headers-$(uname -r)` 安装内核头文件。

------

## 6️⃣ 🔍 内核参数调优（sysctl）

许多内核参数可以通过 `sysctl` 实时调整：

查看所有参数：

```bash
sysctl -a
```

修改参数（临时）：

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

永久修改：

```bash
sudo vim /etc/sysctl.conf
```

添加：

```bash
net.ipv4.ip_forward = 1
vm.swappiness = 10
```

应用修改：

```bash
sudo sysctl -p
```

------

## 7️⃣ 🔧 常用内核调试文件

Linux 提供 `/proc` 虚拟文件系统，用于查看内核状态：

| 路径               | 内容                 |
| ------------------ | -------------------- |
| `/proc/cpuinfo`    | CPU 信息             |
| `/proc/meminfo`    | 内存使用             |
| `/proc/modules`    | 当前已加载模块       |
| `/proc/interrupts` | 中断分布             |
| `/proc/sys/`       | 所有内核可调参数目录 |

> `/proc` 不是磁盘上的真实文件，而是内核暴露的数据接口。

------

## ✅ 本节总结

| 模块       | 内容               | 命令 / 文件                          |
| ---------- | ------------------ | ------------------------------------ |
| 内核版本   | 核心组件信息       | `uname -a`                           |
| 模块管理   | 动态驱动加载       | `lsmod`, `modprobe`, `rmmod`         |
| 配置文件   | 控制模块与编译选项 | `/boot/config-*`, `/etc/modprobe.d/` |
| 参数调优   | 内核行为调整       | `/etc/sysctl.conf`, `sysctl -p`      |
| 编译与更新 | 定制内核           | `make menuconfig` → `make install`   |

------

💡 **一句话记忆：**

> “内核是大脑，模块是神经，`sysctl` 是调节器，`/proc` 是探针。”