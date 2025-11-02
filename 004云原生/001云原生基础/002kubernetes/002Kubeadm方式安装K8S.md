## 一、安装环境

### 1. 安装说明

本次以二进制方式安装高可用 k8s 1.28.0 版本，但在生产环境中，建议使用小版本大于 5 的 Kubernetes 版本，比如 1.19.5 以后。

### 2. 系统环境

| **系统** | **主机名** | **IP 地址**   | **角色**    | **配置**              |
| -------- | ---------- | ------------- | ----------- | --------------------- |
| Centos   | k8s-master | 192.168.15.11 | master 节点 | 4 核 4G 内存 50G 硬盘 |
| Centos   | k8s-node01 | 192.168.15.22 | work 节点   | 4 核 4G 内存 50G 硬盘 |
| Centos   | k8s-node02 | 192.168.15.33 | work 节点   | 4 核 4G 内存 50G 硬盘 |

### 3. 网络及版本环境

| **配置信息** | **备注**      |
| ------------ | ------------- |
| Docker 版本  | 24.10         |
| Pod 网段     | 172.16.0.0/16 |
| Service 网段 | 10.96.0.0/16  |

**注：宿主机网段、Pod 网段、Service 网段不能重复，服务器 IP 地址不能设置为 DHCP，需配置为静态 IP。**

------

## 二、前期准备

### 1. 配置主机映射

```shell
[root@k8s-master ~]# vim /etc/hosts 

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.15.11 k8s-master
192.168.15.11 k8s-master-lb		# 若有高可用主机，这里为另一个master的IP
192.168.15.22 k8s-node01
192.168.15.33 k8s-node02
```

------

### 2. 配置 yum 源

```shell
[root@k8s-master ~]# cd /etc/yum.repos.d/
[root@k8s-master yum.repos.d]# mkdir bak
[root@k8s-master yum.repos.d]# mv Rocky* bak
[root@k8s-master yum.repos.d]# mv local.repo bak
[root@k8s-master yum.repos.d]# ls
aliyunbak  bak
[root@k8s-master yum.repos.d]# vim docker-ce.repo		# docker软件源
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-debuginfo]
name=Docker CE Stable - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-source]
name=Docker CE Stable - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test]
name=Docker CE Test - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-debuginfo]
name=Docker CE Test - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-source]
name=Docker CE Test - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly]
name=Docker CE Nightly - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-debuginfo]
name=Docker CE Nightly - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-source]
name=Docker CE Nightly - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[root@k8s-master yum.repos.d]# vim Rocky-BaseOS.repo		# 阿里云软件源
[baseos]
name=Rocky Linux $releasever - BaseOS
#mirrorlist=https://mirrors.rockylinux.org/mirrorlist?arch=$basearch&repo=BaseOS-$releasever
baseurl=https://mirrors.aliyun.com/rockylinux/$releasever/BaseOS/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rockyofficial

[root@k8s-master yum.repos.d]# vim Rocky-AppStream.repo		# 阿里云软件源
[appstream]
name=Rocky Linux $releasever - AppStream
#mirrorlist=https://mirrors.rockylinux.org/mirrorlist?arch=$basearch&repo=AppStream-$releasever
baseurl=https://mirrors.aliyun.com/rockylinux/$releasever/AppStream/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rockyofficial

[root@k8s-master yum.repos.d]# vim kubernetes.repo		# K8S软件源
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg 	https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
[root@k8s-master yum.repos.d]# ls
aliyunbak  docker-ce.repo   Rocky-AppStream.repo
bak        kubernetes.repo  Rocky-BaseOS.repo
[root@k8s-master yum.repos.d]# yum clean all		# 清除yum缓存
35 文件已删除
[root@k8s-master yum.repos.d]# yum makecache 		# 建立yum元数据缓存
Rocky Linux 8 - AppStream                           5.2 MB/s | 9.6 MB     00:01    
Rocky Linux 8 - BaseOS                              2.6 MB/s | 3.9 MB     00:01    
Docker CE Stable - x86_64                            54 kB/s |  52 kB     00:00    
Kubernetes                                          193 kB/s | 182 kB     00:00    
元数据缓存已建立。
```

------

### 3. 安装必备工具

```shell
[root@k8s-master yum.repos.d]# cd
[root@k8s-master ~]# yum install wget jq psmisc vim net-tools telnet yum-utils device-mapper-persistent-data lvm2 git -y
......省略部分内容......
已安装:
  git-2.39.3-1.el8_8.x86_64                git-core-2.39.3-1.el8_8.x86_64          
  git-core-doc-2.39.3-1.el8_8.noarch       perl-Error-1:0.17025-2.el8.noarch       
  perl-Git-2.39.3-1.el8_8.noarch           perl-TermReadKey-2.37-7.el8.x86_64      
  telnet-1:0.17-76.el8.x86_64              yum-utils-4.0.21-23.el8.noarch          

完毕！
```

------

### 4. 关闭安全及 swap 分区

```shell
[root@k8s-master ~]# systemctl disable --now firewalld
[root@k8s-master ~]# systemctl disable --now dnsmasq
[root@k8s-master ~]# setenforce 0
[root@k8s-master ~]# sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/sysconfig/selinux
[root@k8s-master ~]# sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config
    [root@k8s-master ~]# swapoff -a && sysctl -w vm.swappiness=0
vm.swappiness = 0	
[root@k8s-master ~]# sed -ri '/^[^#]*swap/s@^@#@' /etc/fstab
```

------

### 5. 同步时间

```shell
[root@k8s-master ~]# rpm -ivh https://mirrors.wlnmp.com/rockylinux/wlnmp-release-rocky-8.noarch.rpm
获取https://mirrors.wlnmp.com/rockylinux/wlnmp-release-rocky-8.noarch.rpm
Verifying...                          ################################# [100%]
准备中...                          ################################# [100%]
正在升级/安装...
   1:wlnmp-release-rocky-1-1          ################################# [100%]
[root@k8s-master ~]# yum -y install wntp
[root@k8s-master ~]# ntpdate time2.aliyun.com
19 Dec 21:02:09 ntpdate[33790]: adjust time server 203.107.6.88 offset -0.001396 sec
[root@k8s-master ~]# crontab -e
    */5 * * * * /usr/sbin/ntpdate time2.aliyun.com
```

------

### 6. 配置 limit

```shell
[root@k8s-master ~]# ulimit -SHn 65535		# 单个进程可以打开的文件数量将被限制为 65535
[root@k8s-master ~]# vim /etc/security/limits.conf
# 末尾添加如下内容
* soft nofile 65536
* hard nofile 131072
* soft nproc 65535
* hard nproc 655350
* soft memlock unlimited
* hard memlock unlimited
```

------

### 7. 配置免密登录

```shell
[root@k8s-master ~]# ssh-keygen -t rsa
# 遵循默认配置，一路回车即可
[root@k8s-master ~]# for i in k8s-node01 k8s-node02;do ssh-copy-id -i .ssh/id_rsa.pub $i;done
# 按照提示输入yes和密码
```

------

### 8. 安装 k8s 高可用性 Git 仓库

```shell
[root@k8s-master ~]# cd /root/ ; git clone https://gitee.com/dukuan/k8s-ha-install.git		
# 在 /root/ 目录下克隆一个名为 k8s-ha-install.git 的 Git 仓库
正克隆到 'k8s-ha-install'...
remote: Enumerating objects: 882, done.
remote: Counting objects: 100% (208/208), done.
remote: Compressing objects: 100% (130/130), done.
remote: Total 882 (delta 92), reused 145 (delta 52), pack-reused 674
接收对象中: 100% (882/882), 19.71 MiB | 2.82 MiB/s, 完成.
处理 delta 中: 100% (356/356), 完成.
```

------

### 9. 升级系统并重启

更新系统但不包括内核

```shell
[root@k8s-master ~]# yum update -y --exclude=kernel* --nobest && reboot
```

------

## 三、配置内核模块

### 1. 配置 ipvs 模块

```shell
[root@k8s-master ~]# yum install ipvsadm ipset sysstat conntrack libseccomp -y
[root@k8s-master ~]# modprobe -- ip_vs					# 使用 modprobe 命令加载内核模块，核心 IPVS 模块。
[root@k8s-master ~]# modprobe -- ip_vs_rr				# IPVS 负载均衡算法 rr
[root@k8s-master ~]# modprobe -- ip_vs_wrr			# IPVS 负载均衡算法 wrr
[root@k8s-master ~]# modprobe -- ip_vs_sh				# 用于源端负载均衡的模块
    [root@k8s-master ~]# modprobe -- nf_conntrack		# 用于网络流量过滤和跟踪的模块
[root@k8s-master ~]# vim /etc/modules-load.d/ipvs.conf
# 在系统启动时加载下列 IPVS 和相关功能所需的模块
ip_vs					# 负载均衡模块
ip_vs_lc			# 用于实现基于连接数量的负载均衡算法
ip_vs_wlc			# 用于实现带权重的最少连接算法的模块
ip_vs_rr			# 负载均衡rr算法模块
ip_vs_wrr			# 负载均衡wrr算法模块
ip_vs_lblc		# 负载均衡算法，它结合了最少连接（LC）算法和基于偏置的轮询（Round Robin with Bias）算法
ip_vs_lblcr		# 用于实现基于链路层拥塞状况的最少连接负载调度算法的模块
ip_vs_dh			# 用于实现基于散列（Hashing）的负载均衡算法的模块
ip_vs_sh			# 用于源端负载均衡的模块
ip_vs_fo			# 用于实现基于本地服务的负载均衡算法的模块
ip_vs_nq			# 用于实现NQ算法的模块
ip_vs_sed			# 用于实现随机早期检测（Random Early Detection）算法的模块
ip_vs_ftp			# 用于实现FTP服务的负载均衡模块
ip_vs_sh
nf_conntrack	# 用于跟踪网络连接的状态的模块
ip_tables			# 用于管理防护墙的机制
ip_set				# 用于创建和管理IP集合的模块
xt_set				# 用于处理IP数据包集合的模块，提供了与iptables等网络工具的接口
ipt_set				# 用于处理iptables规则集合的模块
ipt_rpfilter	# 用于实现路由反向路径过滤的模块
ipt_REJECT		# iptables模块之一，用于将不符合规则的数据包拒绝，并返回特定的错误码
ipip					# 用于实现IP隧道功能的模块，使得数据可以在两个网络之间进行传输
[root@k8s-master ~]# systemctl enable --now systemd-modules-load.service		# 开机自启systemd默认提供的网络管理服务
The unit files have no installation config (WantedBy, RequiredBy, Also, Alias
settings in the [Install] section, and DefaultInstance for template units).
This means they are not meant to be enabled using systemctl.
Possible reasons for having this kind of units are:
1) A unit may be statically enabled by being symlinked from another unit's
   .wants/ or .requires/ directory.
2) A unit's purpose may be to act as a helper for some other unit which has
   a requirement dependency on it.
3) A unit may be started when needed via activation (socket, path, timer,
   D-Bus, udev, scripted systemctl call, ...).
4) In case of template units, the unit is meant to be enabled with some
   instance name specified.
[root@k8s-master ~]# lsmod | grep -e ip_vs -e nf_conntrack		# 查看已写入加载的模块
ip_vs_sh               16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs                 172032  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack          172032  4 xt_conntrack,nf_nat,ipt_MASQUERADE,ip_vs
nf_defrag_ipv6         20480  2 nf_conntrack,ip_vs
nf_defrag_ipv4         16384  1 nf_conntrack
libcrc32c              16384  5 nf_conntrack,nf_nat,nf_tables,xfs,ip_vs
```

------

### 2. 配置 k8s 内核

```shell
[root@k8s-master ~]# vim /etc/sysctl.d/k8s.conf
# 写入k8s所需内核模块
net.bridge.bridge-nf-call-iptables = 1		# 控制网络桥接与iptables之间的网络转发行为
net.bridge.bridge-nf-call-ip6tables = 1		# 用于控制网络桥接（bridge）的IP6tables过滤规则。当该参数设置为1时，表示启用对网络桥接的IP6tables过滤规则
fs.may_detach_mounts = 1		# 用于控制文件系统是否允许分离挂载，1表示允许
net.ipv4.conf.all.route_localnet = 1		# 允许本地网络上的路由。设置为1表示允许，设置为0表示禁止。
vm.overcommit_memory=1		# 控制内存分配策略。设置为1表示允许内存过量分配，设置为0表示不允许。
vm.panic_on_oom=0		# 决定当系统遇到内存不足（OOM）时是否产生panic。设置为0表示不产生panic，设置为1表示产生panic。
fs.inotify.max_user_watches=89100		# inotify可以监视的文件和目录的最大数量。
fs.file-max=52706963		# 系统级别的文件描述符的最大数量。
fs.nr_open=52706963			# 单个进程可以打开的文件描述符的最大数量。
net.netfilter.nf_conntrack_max=2310720		# 网络连接跟踪表的最大大小。
net.ipv4.tcp_keepalive_time = 600		# TCP保活机制发送探测包的间隔时间（秒）。
net.ipv4.tcp_keepalive_probes = 3		# TCP保活机制发送探测包的最大次数。
net.ipv4.tcp_keepalive_intvl =15		# TCP保活机制在发送下一个探测包之前等待响应的时间（秒）。
net.ipv4.tcp_max_tw_buckets = 36000	# TCP TIME_WAIT状态的bucket数量。
net.ipv4.tcp_tw_reuse = 1		# 允许重用TIME_WAIT套接字。设置为1表示允许，设置为0表示不允许。
net.ipv4.tcp_max_orphans = 327680		# 系统中最大的孤套接字数量。
net.ipv4.tcp_orphan_retries = 3			# 系统尝试重新分配孤套接字的次数。
net.ipv4.tcp_syncookies = 1		# 用于防止SYN洪水攻击。设置为1表示启用SYN cookies，设置为0表示禁用。
net.ipv4.tcp_max_syn_backlog = 16384		# SYN连接请求队列的最大长度。
net.ipv4.ip_conntrack_max = 65536		# IP连接跟踪表的最大大小。
net.ipv4.tcp_max_syn_backlog = 16384		# 系统中最大的监听队列的长度。
net.ipv4.tcp_timestamps = 0		# 用于关闭TCP时间戳选项。
net.core.somaxconn = 16384		# 用于设置系统中最大的监听队列的长度
[root@k8s-master ~]# reboot
# 保存后，所有节点重启，保证重启后内核依然加载
[root@k8s-master ~]# lsmod | grep --color=auto -e ip_vs -e nf_conntrack
ip_vs_ftp              16384  0
nf_nat                 45056  3 ipt_MASQUERADE,nft_chain_nat,ip_vs_ftp
ip_vs_sed              16384  0
ip_vs_nq               16384  0
ip_vs_fo               16384  0
ip_vs_sh               16384  0
ip_vs_dh               16384  0
ip_vs_lblcr            16384  0
ip_vs_lblc             16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs_wlc              16384  0
ip_vs_lc               16384  0
ip_vs                 172032  25 ip_vs_wlc,ip_vs_rr,ip_vs_dh,ip_vs_lblcr,ip_vs_sh,ip_vs_fo,ip_vs_nq,ip_vs_lblc,ip_vs_wrr,ip_vs_lc,ip_vs_sed,ip_vs_ftp
nf_conntrack          172032  4 xt_conntrack,nf_nat,ipt_MASQUERADE,ip_vs
nf_defrag_ipv6         20480  2 nf_conntrack,ip_vs
nf_defrag_ipv4         16384  1 nf_conntrack
libcrc32c              16384  5 nf_conntrack,nf_nat,nf_tables,xfs,ip_vs
```

------

## 四、基本组件安装

### 1. 安装 Containerd

#### （1）安装 Docker

```shell
[root@k8s-master ~]# yum remove -y podman runc containerd		# 卸载之前的containerd
[root@k8s-master ~]# yum install docker-ce docker-ce-cli containerd.io -y		# 安装Docker和containerd
```

#### （2）配置 Containerd 所需模块

```shell
    [root@k8s-master ~]# cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
> overlay		# 用于支持Overlay网络文件系统的模块，它可以在现有的文件系统之上创建叠加层，以实现虚拟化、隔离和管理等功能。
> br_netfilter		# 用于containerd的网络过滤模块，它可以对进出容器的网络流量进行过滤和管理。
> EOF
overlay
br_netfilter
[root@k8s-master ~]# modprobe -- overlay
[root@k8s-master ~]# modprobe -- br_netfilter
```

#### （3）配置 Containerd 所需内核

```shell
[root@k8s-master ~]# cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf		# tee：读取的数据写入到一个或多个文件中，同时还将其复制到标准输出
> net.bridge.bridge-nf-call-iptables  = 1		# 用于控制网络桥接是否调用iptables进行包过滤和转发。
> net.ipv4.ip_forward                 = 1		# 路由转发，1为开启
> net.bridge.bridge-nf-call-ip6tables = 1		# 控制是否在桥接接口上调用IPv6的iptables进行数据包过滤和转发。
> EOF
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
[root@k8s-master ~]# sysctl --system 
```

#### （4）Containerd 配置文件

```shell
[root@k8s-master ~]# mkdir -p /etc/containerd
[root@k8s-master ~]# containerd config default | tee /etc/containerd/config.toml		# 读取containerd的配置并保存到/etc/containerd/config.toml
[root@k8s-master ~]# vim /etc/containerd/config.toml
# 找到containerd.runtimes.runc.options模块，添加SystemdCgroup = true，如果已经存在则直接修改
          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            BinaryName = ""
            CriuImagePath = ""
            CriuPath = ""
            CriuWorkPath = ""
            IoGid = 0
            IoUid = 0
            NoNewKeyring = false
            NoPivotRoot = false
            Root = ""
            ShimCgroup = ""
            SystemdCgroup = false		# 没有就添加，有的话就修改
# 找到sandbox_image修改为如下参数
		sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9"
[root@k8s-master ~]# systemctl daemon-reload		# 加载systemctl控制脚本
[root@k8s-master ~]# systemctl enable --now containerd		# 启动containerd并设置开机启动
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /usr/lib/systemd/system/containerd.service.
```

#### （5）配置 crictl 客户端连接的运行位置

```shell
[root@k8s-master ~]# cat > /etc/crictl.yaml <<EOF		# 配置容器运行环境的crictl.yml文件
> runtime-endpoint: unix:///run/containerd/containerd.sock		# 指定了容器运行时的地址为：unix://...
> image-endpoint: unix:///run/containerd/containerd.sock			# 指定了镜像运行时的地址为：unix://...
> timeout: 10			# 设置了超时时间为10秒
    > debug: false		# 关闭调试模式
> EOF
```

------

### 2. 安装 Kubernetes 组件

安装 Kubeadm、Kubelet 和 Kubectl

```shell
[root@k8s-master ~]# yum list kubeadm.x86_64 --showduplicates | sort -r
# 查询最新的Kubernetes版本号
[root@k8s-master ~]# yum install kubeadm-1.28* kubelet-1.28* kubectl-1.28* -y
# 安装1.28最新版本kubeadm、kubelet和kubectl

[root@k8s-master ~]# systemctl daemon-reload
[root@k8s-master ~]# systemctl enable --now kubelet		# 允许开机自启kubelet
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /usr/lib/systemd/system/kubelet.service.
    [root@k8s-master ~]# kubeadm version		# 查看当前安装的kubeadm版本号
kubeadm version: &version.Info{Major:"1", Minor:"28", GitVersion:"v1.28.2", GitCommit:"89a4ea3e1e4ddd7f7572286090359983e0387b2f", GitTreeState:"clean", BuildDate:"2023-09-13T09:34:32Z", GoVersion:"go1.20.8", Compiler:"gc", Platform:"linux/amd64"}
```

------

### 3. Kubernetes 集群初始化

#### （1）Kubeadm 配置文件

```shell
[root@k8s-master ~]# vim kubeadm-config.yaml		# 修改kubeadm配置文件
apiVersion: kubeadm.k8s.io/v1beta3		# 指定Kubernetes配置文件的版本，使用的是kubeadm API的v1beta3版本
bootstrapTokens:		# 定义bootstrap tokens的信息。这些tokens用于在Kubernetes集群初始化过程中进行身份验证
- groups:		# 定义了与此token关联的组
  - system:bootstrappers:kubeadm:default-node-token 
  token: 7t2weq.bjbawausm0jaxury		# bootstrap token的值
  ttl: 24h0m0s		# token的生存时间，这里设置为24小时
  usages:			# 定义token的用途
  - signing		# 数字签名
  - authentication		# 身份验证
kind: InitConfiguration		# 指定配置对象的类型，InitConfiguration：表示这是一个初始化配置
localAPIEndpoint:		# 定义本地API端点的地址和端口
  advertiseAddress: 192.168.15.11
  bindPort: 6443
nodeRegistration:		# 定义节点注册时的配置
  criSocket: unix:///var/run/containerd/containerd.sock		# 容器运行时(CRI)的套接字路径
  name: k8s-master		# 节点的名称
  taints:		# 标记
  - effect: NoSchedule		# 免调度节点
    key: node-role.kubernetes.io/control-plane		# 该节点为控制节点
---
apiServer:		# 定义了API服务器的配置
  certSANs:		# 为API服务器指定了附加的证书主体名称(SAN)，指定IP即可
  - 192.168.15.11
  timeoutForControlPlane: 4m0s		# 控制平面的超时时间，这里设置为4分钟
apiVersion: kubeadm.k8s.io/v1beta3		# 指定API Server版本
certificatesDir: /etc/kubernetes/pki	# 指定了证书的存储目录
clusterName: kubernetes		# 定义了集群的名称为"kubernetes"
controlPlaneEndpoint: 192.168.15.11:6443		# 定义了控制节点的地址和端口
controllerManager: {}		# 控制器管理器的配置，为空表示使用默认配置
etcd:		# 定义了etcd的配置
  local:		# 本地etcd实例
    dataDir: /var/lib/etcd		# 数据目录
imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers		# 指定了Kubernetes使用的镜像仓库的地址，阿里云的镜像仓库。
kind: ClusterConfiguration		# 指定了配置对象的类型，ClusterConfiguration：表示这是一个集群配置
kubernetesVersion: v1.28.2		# 指定了kubernetes的版本
networking:		# 定义了kubernetes集群网络设置
  dnsDomain: cluster.local		# 定义了集群的DNS域为：cluster.local
  podSubnet: 172.16.0.0/16		# 定义了Pod的子网
  serviceSubnet: 10.96.0.0/16	# 定义了服务的子网
scheduler: {}		# 使用默认的调度器行为
[root@k8s-master ~]# kubeadm config migrate --old-config kubeadm-config.yaml --new-config new.yaml
# 将旧的kubeadm配置文件转换为新的格式
```

#### （2）下载组件镜像

```shell
[root@k8s-master ~]# kubeadm config images pull --config /root/new.yaml		# 通过新的配置文件new.yaml从指定的阿里云仓库拉取kubernetes组件镜像
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.28.2
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.28.2
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.28.2
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.28.2
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.5.9-0
[config/images] Pulled registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:v1.10.1
```

#### （3）集群初始化

```shell
[root@k8s-master ~]# kubeadm init --config /root/new.yaml  --upload-certs
You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.15.11:6443 --token 7t2weq.bjbawausm0jaxury \
	--discovery-token-ca-cert-hash sha256:73dc6f8d973fc70818e309386c1bfc5d330c19d52b494c6f88f634a6b1250a2f \
	--control-plane --certificate-key 80fcc505867ccbc6550c18ed11f40e64ecf486d626403823f548dda65c19953d
# 等待初始化后保存这些命令





[root@k8s-master ~]# vim token.txt
kubeadm join 192.168.15.11:6443 --token 7t2weq.bjbawausm0jaxury \
        --discovery-token-ca-cert-hash sha256:73dc6f8d973fc70818e309386c1bfc5d330c19d52b494c6f88f634a6b1250a2f \		# 当需要加入新node节点时，只复制到这即可
        --control-plane --certificate-key 80fcc505867ccbc6550c18ed11f40e64ecf486d626403823f548dda65c19953d		# 当需要高可用master集群时，将整个token复制下来
```

#### （4）加载环境变量

```shell
[root@k8s-master ~]# cat <<EOF >> /root/.bashrc
> export KUBECONFIG=/etc/kubernetes/admin.conf
> EOF
[root@k8s-master ~]# source /root/.bashrc
```

#### （5）查看组件容器状态

- 之前采用初始化安装方式，所有的系统组件均以容器的方式运行并且在 kube-system 命名空间内，此时可以查看 Pod（容器组）状态

```shell
[root@k8s-master ~]# kubectl get po -n kube-system		
NAME                                 READY   STATUS    RESTARTS   AGE
coredns-6554b8b87f-77brw             0/1     Pending   0          6m1s
coredns-6554b8b87f-8hwr4             0/1     Pending   0          6m1s
etcd-k8s-master                      1/1     Running   0          6m16s
kube-apiserver-k8s-master            1/1     Running   0          6m16s
kube-controller-manager-k8s-master   1/1     Running   0          6m16s
kube-proxy-j778p                     1/1     Running   0          6m2s
kube-scheduler-k8s-master            1/1     Running   0          6m16s
# kubectl：k8s控制命令
# get：获取参数
# po：pod缩写
# -n：指定命名空间
# kube-system：命名空间
```

#### （6）初始化失败排查

##### Ⅰ. 初始化重置

如果初始化失败，重置后再次初始化，命令如下**（没有失败不要执行！！！）**

```shell
kubeadm reset -f ; ipvsadm --clear ; rm -rf ~/.kube
```

##### Ⅱ. 多次初始化失败

- 首先排查系统日志

- - CentOS日志路径：/var/log/messages
  - Ubuntu日志路径:/var/log/syslog

- 通过日志找到错误原因
- 最后再检查之前所有的配置文件是否有编写错误，有的配置文件在修改后需要重新载入，可以根据刚才的步骤进行修改及载入，最终确认无误后输入重置命令，再进行初始化。

```shell
tail -f /var/log/messages | grep -v "not found"
```

- 经常出错的原因：

- - Containerd 的配置文件修改的不对，自行参考《安装containerd》小节核对。
  - new.yaml 配置问题，比如非高可用集群忘记修改 6443 端口为 6443。
  - new.yaml 配置问题，三个网段有交叉，出现 IP 地址冲突。
  - VIP 不通导致无法初始化成功，此时 messages 日志会有 VIP 超时的报错。

##### Ⅲ.连接 API 服务器超时

当获取集群状态出现以下信息时：

```shell
[root@master ~]# kubectl get po
E1221 14:39:38.091729    2782 memcache.go:265] couldn't get current server API group list: Get "https://192.168.15.11:6443/api?timeout=32s": dial tcp 192.168.15.11:6443: connect: connection refused
E1221 14:39:38.092239    2782 memcache.go:265] couldn't get current server API group list: Get "https://192.168.15.11:6443/api?timeout=32s": dial tcp 192.168.15.11:6443: connect: connection refused
E1221 14:39:38.094041    2782 memcache.go:265] couldn't get current server API group list: Get "https://192.168.15.11:6443/api?timeout=32s": dial tcp 192.168.15.11:6443: connect: connection refused
E1221 14:39:38.095440    2782 memcache.go:265] couldn't get current server API group list: Get "https://192.168.15.11:6443/api?timeout=32s": dial tcp 192.168.15.11:6443: connect: connection refused
E1221 14:39:38.097007    2782 memcache.go:265] couldn't get current server API group list: Get "https://192.168.15.11:6443/api?timeout=32s": dial tcp 192.168.15.11:6443: connect: connection refused
The connection to the server 192.168.15.11:6443 was refused - did you specify the right host or port?
```

此时可以修改系统环境变量

临时修改：

```shell
export KUBECONFIG=/etc/kubernetes/admin.conf
```

长期修改：

```shell
mkdir ~/.kube
cp /etc/kubernetes/admin.conf ~/.kube/config
```

如果修改环境变量后也不行时，需要重新进行初始化（依次执行下面两条命令）

```shell
kubeadm reset -f ; ipvsadm --clear ; rm -rf ~/.kube
kubeadm init --config /root/new.yaml  --upload-certs
```

------

### 4. Master 高可用

- 其他 master 加入集群时，输入如下命令
- 如：需要高可用时，又克隆了 master02、03...等，那么这些节点都执行下面的命令
- **注意：**每个主机的 token 值是不一样的，下面是我 192.168.15.11 (master)主机的 token 值，这是集群初始化生成的代码，需要在当时记录下来。

```shell
kubeadm join 192.168.15.11:6443 --token 7t2weq.bjbawausm0jaxury \
--discovery-token-ca-cert-hash \
sha256:73dc6f8d973fc70818e309386c1bfc5d330c19d52b494c6f88f634a6b1250a2f \
--control-plane --certificate-key \
80fcc505867ccbc6550c18ed11f40e64ecf486d626403823f548dda65c19953d
```

------

### 5. Token 过期处理

**注意：****以下步骤是上述初始化命令产生的 Token 过期了才需要执行以下步骤，****如果没有过期不需要执行，直接 join 即可。**

Token 过期后生成新的 token

```shell
kubeadm token create --print-join-command
```

Master 需要生成 --certificate-key：

```shell
kubeadm init phase upload-certs  --upload-certs
```

### 6. Node 节点配置

- Node 节点上主要部署公司的一些业务应用，生产环境中不建议 Master 节点部署系统组件之外的其他 Pod，测试环境可以允许 Master 节点部署 Pod 以节省系统资源。

#### （1）node 加入集群

```shell
[root@k8s-node01 ~]# kubeadm join 192.168.15.11:6443 --token 7t2weq.bjbawausm0jaxury \		# node01通过复制master初始化生成的token来加入集群
> --discovery-token-ca-cert-hash \
> sha256:73dc6f8d973fc70818e309386c1bfc5d330c19d52b494c6f88f634a6b1250a2f
[preflight] Running pre-flight checks
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
# 正确加入集群后的输出信息
```

#### （2）查看集群状态

master 上查看集群状态（NotReady 不影响）

```shell
[root@k8s-master ~]# kubectl get node		# 获取所有节点信息
NAME         STATUS     ROLES           AGE     VERSION
k8s-master   NotReady   control-plane   35m     v1.28.2
k8s-node01   NotReady   <none>   				6m39s   v1.28.2
k8s-node02   NotReady   <none>   				7m27s   v1.28.2
```

**到此建议打快照**

------

### 7. Calico 组件安装

#### （1）切换 git 分支

```shell
[root@k8s-master ~]# cd /root/k8s-ha-install && git checkout manual-installation-v1.28.x && cd calico/
分支 'manual-installation-v1.28.x' 设置为跟踪 'origin/manual-installation-v1.28.x'。
切换到一个新分支 'manual-installation-v1.28.x'
```

#### （2）修改 Pod 网段

```shell
[root@k8s-master calico]# POD_SUBNET=`cat /etc/kubernetes/manifests/kube-controller-manager.yaml | grep cluster-cidr= | awk -F= '{print $NF}'`		# 获取已定义的Pod网段
[root@k8s-master calico]# sed -i "s#POD_CIDR#${POD_SUBNET}#g" calico.yaml		# 修改calico.yml文件中的pod网段
[root@k8s-master calico]# kubectl apply -f calico.yaml		# 创建calico的pod
```

#### （3）查看容器和节点状态

```shell
[root@k8s-master calico]# kubectl get po -n kube-system
NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-6d48795585-wj8g5   1/1     Running   0          130m
calico-node-bk4p5                          1/1     Running   0          130m
calico-node-kmsh7                          1/1     Running   0          130m
calico-node-qthgh                          1/1     Running   0          130m
coredns-6554b8b87f-jdc2b                   1/1     Running   0          133m
coredns-6554b8b87f-thftb                   1/1     Running   0          133m
etcd-master                                1/1     Running   0          133m
kube-apiserver-master                      1/1     Running   0          133m
kube-controller-manager-master             1/1     Running   0          133m
kube-proxy-46j4z                           1/1     Running   0          131m
kube-proxy-8g887                           1/1     Running   0          133m
kube-proxy-vwp27                           1/1     Running   0          131m
kube-scheduler-master                      1/1     Running   0          133m
[root@k8s-master calico]# kubectl get node		# 此时节点全部准备完成
NAME         STATUS   ROLES           AGE   VERSION
k8s-master   Ready    control-plane   40m   v1.28.2
k8s-node01   Ready    <none>   				12m   v1.28.2
k8s-node02   Ready    <none>   				12m   v1.28.2
```

------

### 8. Metrics 部署

在新版的 Kubernetes 中系统资源的采集均使用 Metrics-server，可以通过 Metrics 采集节点和 Pod 的内存、磁盘、CPU和网络的使用率。

#### （1）复制证书到所有 node 节点

将 master 节点的 front-proxy-ca.crt 复制到所有 Node 节点，每有一个节点执行一次，仅需修改命令内的 node 节点主机名即可。

```shell
[root@k8s-master calico]# scp /etc/kubernetes/pki/front-proxy-ca.crt k8s-node01:/etc/kubernetes/pki/front-proxy-ca.crt		# 向node01节点发送代理证书
front-proxy-ca.crt                                100% 1123   937.0KB/s   00:00    
[root@k8s-master calico]# scp /etc/kubernetes/pki/front-proxy-ca.crt k8s-node02:/etc/kubernetes/pki/front-proxy-ca.crt		# 向node02节点发送代理证书
front-proxy-ca.crt                                100% 1123   957.4KB/s   00:00    

# 若有其他node节点，按照格式执行下面命令，这里不用执行，因为node只有两台主机
[root@k8s-master calico]# scp /etc/kubernetes/pki/front-proxy-ca.crt k8s-node03:/etc/kubernetes/pki/front-proxy-ca.crt
```

#### （2）安装 metrics server

```shell
[root@k8s-master calico]# cd /root/k8s-ha-install/kubeadm-metrics-server
[root@k8s-master kubeadm-metrics-server]# kubectl  create -f comp.yaml		# 添加metric server的pod资源
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
```

#### （3）查看 metrics server 状态

```shell
[root@master kubeadm-metrics-server]# kubectl get po -n kube-system -l k8s-app=metrics-server		# 在kube-system命名空间下查看metrics server的pod运行状态
NAME                             READY   STATUS    RESTARTS   AGE
metrics-server-8df99c47f-mkbfd   1/1     Running   0          34s
[root@master kubeadm-metrics-server]# kubectl top node		# 查看node节点的系统资源使用情况
NAME         CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
k8s-node01   51m          1%     831Mi           23%       
k8s-node02   55m          1%     931Mi           25%       
master       107m         2%     1412Mi          39%       
[root@master kubeadm-metrics-server]# kubectl top po -A
NAMESPACE     NAME                                       CPU(cores)   MEMORY(bytes)   
kube-system   calico-kube-controllers-6d48795585-wj8g5   2m           25Mi            
kube-system   calico-node-bk4p5                          20m          155Mi           
kube-system   calico-node-kmsh7                          25m          152Mi           
kube-system   calico-node-qthgh                          24m          145Mi           
kube-system   coredns-6554b8b87f-jdc2b                   1m           22Mi            
kube-system   coredns-6554b8b87f-thftb                   1m           20Mi            
kube-system   etcd-master                                14m          66Mi            
kube-system   kube-apiserver-master                      29m          301Mi           
kube-system   kube-controller-manager-master             10m          56Mi            
kube-system   kube-proxy-46j4z           0                1m           22Mi            
kube-system   kube-proxy-8g887                           1m           24Mi            
kube-system   kube-proxy-vwp27                           1m           22Mi            
kube-system   kube-scheduler-master                      2m           26Mi            
kube-system   metrics-server-8df99c47f-mkbfd             3m           29Mi            
```

------

### 9. Dashboard部署

Dashboard 用于展示集群中的各类资源，同时也可以通过 Dashboard 实时查看 Pod 的日志和在容器中执行一些命令等。

#### （1）安装组件

```shell
[root@master kubeadm-metrics-server]# cd /root/k8s-ha-install/dashboard/
[root@master dashboard]# kubectl create -f .		# 建立dashboard的pod资源
serviceaccount/admin-user created
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

#### （2）登录 dashboard

- 如果是谷歌浏览器，需要在启动文件中加入下面的启动参数，用于解决无法访问 Dashboard 的问题
- --test-type --ignore-certificate-errors
- ![img](./assets/1703052669217-a5a7bbf5-1166-459d-ab9f-69eb92c3dee4.png)

#### （3）更改 svc 模式

> ‌**[SVC模式](https://www.baidu.com/s?sa=re_dqa_generate&wd=SVC模式&rsv_pq=e99603690003441d&oq=什么是svc模式&rsv_t=76663f8XnI6iWUoKiOW1sVhuEn5wInz4TxVl2/OkTLQAjZNs+OoHdTyvmjJPGk3AhAaO&tn=baiduhome_pg&ie=utf-8)是[ARM处理器](https://www.baidu.com/s?sa=re_dqa_generate&wd=ARM处理器&rsv_pq=e99603690003441d&oq=什么是svc模式&rsv_t=76663f8XnI6iWUoKiOW1sVhuEn5wInz4TxVl2/OkTLQAjZNs+OoHdTyvmjJPGk3AhAaO&tn=baiduhome_pg&ie=utf-8)的一种工作模式，也被称为[管理模式](https://www.baidu.com/s?sa=re_dqa_generate&wd=管理模式&rsv_pq=e99603690003441d&oq=什么是svc模式&rsv_t=76663f8XnI6iWUoKiOW1sVhuEn5wInz4TxVl2/OkTLQAjZNs+OoHdTyvmjJPGk3AhAaO&tn=baiduhome_pg&ie=utf-8)。**‌
>
> SVC模式是ARM处理器中的一种特权模式，用于操作系统保护。当处理器执行复位或软中断指令时，将进入SVC模式。这种模式允许操作系统执行一些特权操作，如访问某些受保护的内存区域或执行系统调用，从而实现对系统的控制和资源管理。SVC模式提供了一种安全的方式来处理系统级任务，确保只有经过授权的操作可以在这种模式下执行，从而保护系统的稳定性和安全性。
>
> 在SVC模式下，处理器使用特定的寄存器来进行操作，这些寄存器的使用和保护机制确保了只有合法的操作系统代码可以执行关键的系统级任务。例如，[寄存器13](https://www.baidu.com/s?sa=re_dqa_generate&wd=寄存器13&rsv_pq=e99603690003441d&oq=什么是svc模式&rsv_t=76663f8XnI6iWUoKiOW1sVhuEn5wInz4TxVl2/OkTLQAjZNs+OoHdTyvmjJPGk3AhAaO&tn=baiduhome_pg&ie=utf-8)通常用作[OS栈指针](https://www.baidu.com/s?sa=re_dqa_generate&wd=OS栈指针&rsv_pq=e99603690003441d&oq=什么是svc模式&rsv_t=76663f8XnI6iWUoKiOW1sVhuEn5wInz4TxVl2/OkTLQAjZNs+OoHdTyvmjJPGk3AhAaO&tn=baiduhome_pg&ie=utf-8)，但也可以用作通用寄存器，这为操作系统提供了灵活性，同时确保了系统状态的安全管理。
>
> SVC模式的存在是为了在操作系统和硬件之间提供一个明确的接口，使得操作系统能够安全、有效地管理硬件资源，同时保护用户模式下的应用程序免受潜在的干扰或破坏。这种模式的设计有助于实现一个稳定、安全的操作系统环境，特别是在多任务环境和复杂的系统配置中尤为重要‌12。

```shell
[root@master dashboard]# kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard		
# edit：进入kubernetes的文本编辑器
# svc：指定某个服务项，这里指定的是kubernetes-dashboard
# -n：指定命名空间，kubernetes-dashboard
# 命令执行后相当于进入vim文本编辑器，不要用鼠标滚轮，会输出乱码的！可以使用“/”搜索，输入“/type”找到目标，如果已经为NodePort忽略此步骤
......省略部分内容......
  selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: NodePort
```

图示：

![img](./assets/1703052844028-1a163e70-5046-4cac-b848-b910f22d5de5.png)

#### （4）查看访问端口号

```shell
[root@master dashboard]# kubectl get svc kubernetes-dashboard -n kubernetes-dashboard		# 获取kubernetes-dashboard状态信息，包含端口，服务IP等
NAME                   TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard   NodePort   10.96.137.94   <none>        443:30582/TCP   8m50s
```

找到端口号后，通过 **master 的 IP+端口**即可访问 dashboard（端口为终端查询到的端口，要用 https 协议访问）

![img](./assets/1703053156139-ea468f9d-4e3e-4402-a5ad-275d789469e7.png)

#### （5）创建登录 token

```shell
[root@master dashboard]# kubectl create token admin-user -n kube-system
eyJhbGciOiJSUzI1NiIsImtpZCI6Inlvc2g1cWhWcjduaXI1ZUFpQWNwRFJYYW1saXVFM3lrdlJnaHlUSmY0RTAifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzAzMDU2Nzg4LCJpYXQiOjE3MDMwNTMxODgsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNzE0YWU1N2UtNjRiNC00NTU0LTk5OTctYjE2NmEwZTQyNzhjIn19LCJuYmYiOjE3MDMwNTMxODgsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTphZG1pbi11c2VyIn0.l6zgXfNhppndKAqFJrR_vCi7w0_esGp7yQGNYdrQGlE5latyFKRXNJub8dvDe-ZyquW1H-KTvZntAluwOXv79W-KY8Z8d31FePN9LHzCXPDordzyg8rE7qvgAPNeU8Fg-VnYtr_ujpB-muBinjnzT7LjysJiBi6fsndiD5RUYcYr6bsLg91bcLgAdW3bn_9W5587z_q-910wpxl9AwUL9xVzy-vsVDDdXe1VthkoGYxyaznRf5omkmpwabQ3JQ0L8U_8Oop6HaZsg5cEBCqBHrgyjBsYRALjzRlFlC9CB4hrYY4P_zRSdoI0lyiG4Zeh0ber6awoeeKSMbJMTqwMlw
```

在“输入 token *”内输入终端生成的 token

![img](./assets/1703053228678-160036b7-646a-43b5-a233-15328ed551aa.png)

------

### 10. Kube-proxy

#### （1）改为 ipvs模式

```shell
[root@master ~]# kubectl edit cm kube-proxy -n kube-system
# 使用“/”找到“mode”，按照如下修改
  mode: ipvs
```

#### （2）更新 Kube-Proxy 的 Pod

```shell
[root@master ~]# kubectl patch daemonset kube-proxy -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"date\":\"`date +'%s'`\"}}}}}" -n kube-system
daemonset.apps/kube-proxy patched
[root@master ~]# curl 127.0.0.1:10249/proxyMode
ipvs
```

------

## 五、集群可用性验证

### 1. 验证节点

```shell
[root@master ~]# kubectl get node		# 全部为Ready，是正常
NAME         STATUS   ROLES           AGE    VERSION
k8s-node01   Ready    <none>          156m   v1.28.2
k8s-node02   Ready    <none>          155m   v1.28.2
master       Ready    control-plane   157m   v1.28.2
```

### 2. 验证 Pod

```shell
[root@master ~]# kubectl get po -A		# 全部为running，表示正常
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
kube-system            calico-kube-controllers-6d48795585-wj8g5     1/1     Running   0          156m
kube-system            calico-node-bk4p5                            1/1     Running   0          156m
kube-system            calico-node-kmsh7                            1/1     Running   0          156m
kube-system            calico-node-qthgh                            1/1     Running   0          156m
kube-system            coredns-6554b8b87f-jdc2b                     1/1     Running   0          159m
kube-system            coredns-6554b8b87f-thftb                     1/1     Running   0          159m
kube-system            etcd-master                                  1/1     Running   0          159m
kube-system            kube-apiserver-master                        1/1     Running   0          159m
kube-system            kube-controller-manager-master               1/1     Running   0          159m
kube-system            kube-proxy-9sxt9                             1/1     Running   0          5m6s
kube-system            kube-proxy-g79z5                             1/1     Running   0          5m7s
kube-system            kube-proxy-scwgn                             1/1     Running   0          5m9s
kube-system            kube-scheduler-master                        1/1     Running   0          159m
kube-system            metrics-server-8df99c47f-mkbfd               1/1     Running   0          154m
kubernetes-dashboard   dashboard-metrics-scraper-7b554c884f-92jwb   1/1     Running   0          24m
kubernetes-dashboard   kubernetes-dashboard-54b699784c-f7trp        1/1     Running   0          24m
```

### 3. 验证集群网段是否冲突

三方网段均不冲突（service、Pod、宿主机）

```shell
[root@master ~]# kubectl get svc		# 查看服务的网段
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   160m
[root@master ~]# kubectl get po -A -owide		# 查看所有命名空间下的所有网段，再与服务的网段进行比较
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE     IP              NODE         NOMINATED NODE   READINESS GATES
kube-system            calico-kube-controllers-6d48795585-wj8g5     1/1     Running   0          158m    172.16.58.194   k8s-node02   <none>           <none>
kube-system            calico-node-bk4p5                            1/1     Running   0          158m    192.168.15.22   k8s-node01   <none>           <none>
kube-system            calico-node-kmsh7                            1/1     Running   0          158m    192.168.15.33   k8s-node02   <none>           <none>
kube-system            calico-node-qthgh                            1/1     Running   0          158m    192.168.15.11   master       <none>           <none>
kube-system            coredns-6554b8b87f-jdc2b                     1/1     Running   0          160m    172.16.58.195   k8s-node02   <none>           <none>
kube-system            coredns-6554b8b87f-thftb                     1/1     Running   0          160m    172.16.58.193   k8s-node02   <none>           <none>
kube-system            etcd-master                                  1/1     Running   0          160m    192.168.15.11   master       <none>           <none>
kube-system            kube-apiserver-master                        1/1     Running   0          160m    192.168.15.11   master       <none>           <none>
kube-system            kube-controller-manager-master               1/1     Running   0          160m    192.168.15.11   master       <none>           <none>
kube-system            kube-proxy-9sxt9                             1/1     Running   0          6m29s   192.168.15.11   master       <none>           <none>
kube-system            kube-proxy-g79z5                             1/1     Running   0          6m30s   192.168.15.33   k8s-node02   <none>           <none>
kube-system            kube-proxy-scwgn                             1/1     Running   0          6m32s   192.168.15.22   k8s-node01   <none>           <none>
kube-system            kube-scheduler-master                        1/1     Running   0          160m    192.168.15.11   master       <none>           <none>
kube-system            metrics-server-8df99c47f-mkbfd               1/1     Running   0          155m    172.16.85.193   k8s-node01   <none>           <none>
kubernetes-dashboard   dashboard-metrics-scraper-7b554c884f-92jwb   1/1     Running   0          25m     172.16.85.195   k8s-node01   <none>           <none>
kubernetes-dashboard   kubernetes-dashboard-54b699784c-f7trp        1/1     Running   0          25m     172.16.85.194   k8s-node01   <none>           <none>
```

### 4. 验证是否可正常创建参数

```shell
[root@master ~]# kubectl create deploy cluster-test --image=registry.cn-beijing.aliyuncs.com/dotbalo/debug-tools -- sleep 3600
deployment.apps/cluster-test created		# 已创建，表示正常
[root@master ~]# kubectl get po
NAME                            READY   STATUS    RESTARTS   AGE
cluster-test-66bb44bd88-sq8fx   1/1     Running   0          41s
[root@master ~]# kubectl get po -owide
NAME                            READY   STATUS    RESTARTS   AGE   IP              NODE         NOMINATED NODE   READINESS GATES
cluster-test-66bb44bd88-sq8fx   1/1     Running   0          48s   172.16.58.196   k8s-node02   <none>           <none>
```

------

### 5. Pod 必须能够解析 Service

同 namespace 和跨 namespace

#### （1）nslookup kubernetes

```shell
[root@master ~]# kubectl exec -it cluster-test-66bb44bd88-sq8fx -- bash		# 进入pod下的某个容器
(06:36 cluster-test-66bb44bd88-sq8fx:/) nslookup kubernetes
Server:		10.96.0.10
Address:	10.96.0.10#53

Name:	kubernetes.default.svc.cluster.local
Address: 10.96.0.1
# 可以解析到server的IP地址说明同namespace可以解析
```

#### （2）nslookup kube-dns.kube-system

```shell
(06:36 cluster-test-66bb44bd88-sq8fx:/) nslookup kube-dns.kube-system
Server:		10.96.0.10
Address:	10.96.0.10#53

Name:	kube-dns.kube-system.svc.cluster.local
Address: 10.96.0.10

# 可以解析到server的第十个ip，说明可以解析到kube-dns，说明跨namespace也可解析
```

### 6. 确认是否可访问 Kubernetes 的 443 和 kube-dns 的 53

- 每个节点都必须能访问 Kubernetes 的 kubernetes svc 443 和 kube-dns 的 service 53

```shell
[root@master ~]# curl https://10.96.0.1:443
curl: (60) SSL certificate problem: unable to get local issuer certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
[root@master ~]# curl 10.96.0.10:53
curl: (52) Empty reply from server
```

### 7. 确认各 Pod 之间是否可正常通信

- 同 namespace 和跨 namespace

```shell
[root@master ~]# kubectl get po -nkube-system -owide
NAME                                       READY   STATUS    RESTARTS   AGE    IP              NODE         NOMINATED NODE   READINESS GATES
calico-kube-controllers-6d48795585-wj8g5   1/1     Running   0          170m   172.16.58.194   k8s-node02   <none>           <none>
calico-node-bk4p5                          1/1     Running   0          170m   192.168.15.22   k8s-node01   <none>           <none>
calico-node-kmsh7                          1/1     Running   0          170m   192.168.15.33   k8s-node02   <none>           <none>
calico-node-qthgh                          1/1     Running   0          170m   192.168.15.11   master       <none>           <none>
coredns-6554b8b87f-jdc2b                   1/1     Running   0          173m   172.16.58.195   k8s-node02   <none>           <none>
coredns-6554b8b87f-thftb                   1/1     Running   0          173m   172.16.58.193   k8s-node02   <none>           <none>
etcd-master                                1/1     Running   0          173m   192.168.15.11   master       <none>           <none>
kube-apiserver-master                      1/1     Running   0          173m   192.168.15.11   master       <none>           <none>
kube-controller-manager-master             1/1     Running   0          173m   192.168.15.11   master       <none>           <none>
kube-proxy-9sxt9                           1/1     Running   0          19m    192.168.15.11   master       <none>           <none>
kube-proxy-g79z5                           1/1     Running   0          19m    192.168.15.33   k8s-node02   <none>           <none>
kube-proxy-scwgn                           1/1     Running   0          19m    192.168.15.22   k8s-node01   <none>           <none>
kube-scheduler-master                      1/1     Running   0          173m   192.168.15.11   master       <none>           <none>
metrics-server-8df99c47f-mkbfd             1/1     Running   0          168m   172.16.85.193   k8s-node01   <none>           <none>
[root@master ~]# kubectl get po -owide
NAME                            READY   STATUS    RESTARTS   AGE   IP              NODE         NOMINATED NODE   READINESS GATES
cluster-test-66bb44bd88-sq8fx   1/1     Running   0          12m   172.16.58.196   k8s-node02   <none>           <none>
[root@master ~]# kubectl exec -it cluster-test-66bb44bd88-sq8fx -- bash
(06:46 cluster-test-66bb44bd88-sq8fx:/) ping 172.16.58.195 -c 3
PING 172.16.58.195 (172.16.58.195) 56(84) bytes of data.
64 bytes from 172.16.58.195: icmp_seq=1 ttl=63 time=0.455 ms
64 bytes from 172.16.58.195: icmp_seq=2 ttl=63 time=0.082 ms
64 bytes from 172.16.58.195: icmp_seq=3 ttl=63 time=0.082 ms

--- 172.16.58.195 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2083ms
rtt min/avg/max/mdev = 0.082/0.206/0.455/0.176 ms
```

- 同机器和跨机器

```shell
[root@master ~]# kubectl get po -owide
NAME                            READY   STATUS    RESTARTS   AGE   IP              NODE         NOMINATED NODE   READINESS GATES
cluster-test-66bb44bd88-sq8fx   1/1     Running   0          13m   172.16.58.196   k8s-node02   <none>           <none>
[root@master ~]# ping 172.16.58.196 -c 3
PING 172.16.58.196 (172.16.58.196) 56(84) bytes of data.
64 bytes from 172.16.58.196: icmp_seq=1 ttl=63 time=0.676 ms
64 bytes from 172.16.58.196: icmp_seq=2 ttl=63 time=0.303 ms
64 bytes from 172.16.58.196: icmp_seq=3 ttl=63 time=0.284 ms

--- 172.16.58.196 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2043ms
rtt min/avg/max/mdev = 0.284/0.421/0.676/0.180 ms
```

------

## 六、注意事项

**注意：**kubeadm 安装的集群，证书有效期默认是一年。master 节点的 kube-apiserver、kube-scheduler、kube-controller-manager、etcd 都是以容器运行的。可以通过 kubectl get po -n kube-system 查看。

启动和二进制不同的是，

kubelet 的配置文件在 /etc/sysconfig/kubelet和/var/lib/kubelet/config.yaml，修改后需要重启 kubelet 进程。

其他组件的配置文件在 /etc/kubernetes/manifests 目录下，比如 kube-apiserver.yaml，该 yaml 文件更改后，kubelet 会自动刷新配置，也就是会重启 pod。不能再次创建该文件

kube-proxy 的配置在 kube-system 命名空间下的 configmap 中，可以通过：

```shell
kubectl edit cm kube-proxy -n kube-system
```

进行更改，更改完成后，可以通过 patch 重启 kube-proxy：

```shell
kubectl patch daemonset kube-proxy -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"date\":\"`date +'%s'`\"}}}}}" -n kube-system
```

Kubeadm 安装后，master 节点默认不允许部署 Pod，可以通过以下方式删除 Taint，即可部署 Pod：

```shell
kubectl taint node -l node-role.kubernetes.io/control-plane node-role.kubernetes.io/control-plane:NoSchedule-
```