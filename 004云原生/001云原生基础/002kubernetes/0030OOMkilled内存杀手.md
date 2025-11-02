# Kubernetes 触发 OOMKilled(内存杀手)如何排除故障

- 简单整一下 k8s 中 Pod 故障 `OOMKilled` 的原因以及诊断

- 博文内容涉及:

- k8s `OOMKilled` 分类: 宿主节点行为 / K8s Cgroups 行为

- 什么是 `OOMKilled` K8s 错误,OOMKiller 机制如何工作？

- `OOMKilled` K8s 错误和解决的常见原因

  

(OOMKilled 即为内存杀手)，当前集群给 Pod 所在进程分配的内存用完了，没有可分配的内存，出于集群稳定考虑， k8s 会委托 `Cgroups` 会把当前 Pod 进程杀掉， 今天和小伙伴分享一些出现 OOMKilled 的原因，以及排故。

有小伙伴可能会问,为什么 CPU 不够不会被 杀掉,内存不够会被杀掉,这是因为 CPU 为可压缩资源,而内存属于不可压缩资源, CPU 不够 cgroups 会对 Pod 中的容器的 CPU 使用进行限流(Throttled). 而内存不够,则会被 Kill 掉.



### k8s OOMKilled 分类

这里的K8s `OOMKilled` 实际上是分两种情况,

- 第一种为宿主节点行为,即 `OOMKillde` 是由宿主机内核直接触发,当 Pod 中没有进行资源限制,会无限制的超用宿主节点资源,触发的 `OOMKillde`.
- 第二种即为今天和小伙伴分享的, K8s 行为,Pod 配置了资源限制,超过了资源限制,由`Cgroups` 触发的 `OOMKillde`

### 宿主节点行为

对于第一种我们简单看一个 Demo,创建一个没有限制资源的pod

```yaml
┌──[root@vms81.liruilongs.github.io]-[~/ansible/resources]
└─$cat pod-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod-demo
  name: pod-demo
spec:
  containers:
  - image: hub.c.163.com/library/centos
    imagePullPolicy: IfNotPresent
    name: pod-demo
    command: ['sh','-c','sleep 500000']
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

pod创建成功后,我们可以看到调度到了`vms83.liruilongs.github.io`

```bash
┌──[root@vms81.liruilongs.github.io]-[~/ansible/resources]
└─$kubectl get pods -o wide
No resources found in resources namespace.
┌──[root@vms81.liruilongs.github.io]-[~/ansible/resources]
└─$vim  pod-demo.yaml
┌──[root@vms81.liruilongs.github.io]-[~/ansible/resources]
└─$kubectl  apply  -f pod-demo.yaml
pod/pod-demo created
┌──[root@vms81.liruilongs.github.io]-[~/ansible/resources]
└─$kubectl get pods -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP             NODE                         NOMINATED NODE   READINESS GATES
pod-demo   1/1     Running   0          42s   10.244.70.50   vms83.liruilongs.github.io   <none>           <none>
```

这里我们在pod里安装一个[内存分配](https://zhida.zhihu.com/search?content_id=232440962&content_type=Article&match_order=1&q=内存分配&zhida_source=entity)工具`bigmem`,用于模拟pod中容器进程内存不回收的情况。

```bash
┌──[root@vms81.liruilongs.github.io]-[~/ansible]
└─$kubectl  cp ./bigmem-7.0-1.r29766.x86_64.rpm pod-demo:/root/
┌──[root@vms81.liruilongs.github.io]-[~/ansible]
└─$kubectl  exec -it pod-demo  -- bin/bash
[root@pod-demo /]# cd root/
[root@pod-demo ~]# ls
anaconda-ks.cfg  bigmem-7.0-1.r29766.x86_64.rpm  original-ks.cfg
[root@pod-demo ~]# rpm -ivh bigmem-7.0-1.r29766.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:bigmem-7.0-1.r29766              ################################# [100%]
[root@pod-demo ~]# bigmem 1000M
Attempting to allocate 1000 Mebibytes of resident memory...
Press <Enter> to exit^C
[root@pod-demo ~]# bigmem 2000M
Attempting to allocate 2000 Mebibytes of resident memory...
Killed
```

通过上下内存信息可以发现，当分配1000M内存时，宿主机用户使用内存增加了1000M，可用内存为117M，当申请内存为2000M时，超出宿主机可用内存，`bigmem 2000M`命令所在进程直接被kill了。

```bash
┌──[root@vms81.liruilongs.github.io]-[~/ansible]
└─$ansible vms83.liruilongs.github.io -m shell  -a "free -h"
vms83.liruilongs.github.io | CHANGED | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           4.4G        2.5G        583M        216M        1.4G        1.4G
Swap:            0B          0B          0B
┌──[root@vms81.liruilongs.github.io]-[~/ansible]
└─$ansible vms83.liruilongs.github.io -m shell  -a "free -h"
vms83.liruilongs.github.io | CHANGED | rc=0 >>
              total        used        free      shared  buff/cache   available
Mem:           4.4G        3.5G        117M        216M        857M        456M
Swap:            0B          0B          0B
```

查看宿主机日志 `/var/log/messages`，可以发现`bigmem` 所在进程造成OOM。被OOM killer 杀掉了。

```bash
┌──[root@vms83.liruilongs.github.io]-[~]
└─$cat /var/log/messages | grep -i memory
Aug 10 20:37:27 vms83 kernel: [<ffffffff81186bd6>] out_of_memory+0x4b6/0x4f0
Aug 10 20:37:27 vms83 kernel: Out of memory: Kill process 25143 (bigmem) score 1347 or sacrifice child
```

### K8s 行为

对于由 k8s 通过 Cgroup 促发的 `OOMKilled` ,我们可以在 `describe` 看到相关信息

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl describe  pods oom-killer-pod | grep -A 10  State
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Mon, 03 Jul 2023 12:17:09 +0800
      Finished:     Mon, 03 Jul 2023 12:17:10 +0800
    Ready:          False
    Restart Count:  5
    Limits:
      memory:  200Mi
    Requests:
      memory:     200Mi
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$
```

对于在生产环境的 Pod ，`OOMKilled` 常常 伴随这 `CrashLoopBackOff`，触发 OOM 之后，被 Kill 掉，之后由于 Pod 重启机制，会陷入 `CrashLoopBackOff`

### 什么是 OOMKilled K8s 错误

当 Kubernetes 集群中的容器超出其内存限制时，Kubernetes 系统可能会终止该容器，并显示“OOMKilled”错误，该错误表示该进程因内存不足而终止。此错误的退出代码为 1，

同样看一个 Demo

当前环境

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl get nodes
NAME                          STATUS   ROLES           AGE    VERSION
vms100.liruilongs.github.io   Ready    control-plane   157d   v1.25.1
vms101.liruilongs.github.io   Ready    control-plane   157d   v1.25.1
vms102.liruilongs.github.io   Ready    control-plane   157d   v1.25.1
vms103.liruilongs.github.io   Ready    <none>          157d   v1.25.1
vms105.liruilongs.github.io   Ready    <none>          157d   v1.25.1
vms106.liruilongs.github.io   Ready    <none>          157d   v1.25.1
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$
```

测试环境准备

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl create  ns oom
namespace/oom created
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl config  set-context --current --namespace=oom
Context "kubernetes-admin@kubernetes" modified.
```

Pod Yaml 文件

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$cat oom-killer-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: oom-killer-pod
spec:
  containers:
  - name: oom-killer-container
    image: polinux/stress
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "200M", "--timeout", "10s","--verbose"]
    resources:
      limits:
        memory: "200Mi"
```

应用测试，查看 `OOMKilled` 的情况

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl apply  -f oom-killer-pod.yaml
pod/oom-killer-pod created
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl get pods oom-killer-pod -w
NAME             READY   STATUS              RESTARTS   AGE
oom-killer-pod   0/1     ContainerCreating   0          7s
oom-killer-pod   1/1     Running             0          25s
oom-killer-pod   0/1     OOMKilled           0          28s
oom-killer-pod   0/1     OOMKilled           1 (18s ago)   45s
oom-killer-pod   0/1     CrashLoopBackOff    1 (13s ago)   57s
oom-killer-pod   0/1     OOMKilled           2 (29s ago)   73s
```

查看对应宿主节点的日志 `/var/log/messages` ,可以看到类似这样一条 `OOMKilled` 日志,即由 `cgroup` 的限制 促发了 `OOMKilled`

```bash
Aug 10 21:09:11 vms106 kernel: Memory cgroup out of memory: Kill process ........
```

### OOMKiller 机制如何工作？

由上面可知,实际上内存杀手 (OOMKiller) 是 Linux 内核(不是本机 Kubernetes)中的一种机制，负责通过杀死消耗过多内存的进程来防止系统内存不足。当系统内存不足时，内核会调用 OOMKiller 来选择要终止的进程，以释放内存并保持系统运行。

OOMKiller 的工作原理是选择消耗最多内存的进程，该进程也被认为对系统操作最不重要。此选择过程基于多个因素，包括进程的内存使用情况、优先级以及运行的时间量。

一旦 OOMKiller 选择要终止的进程，它就会向该进程发送信号，要求它正常终止。如果进程不响应信号，内核将强制终止进程并释放其内存。，如果节点上的 Pod 重启策略设置为“始终”，则由于内存问题而被终止的 Pod 不一定会从节点中逐出，它会尝试重新启动 Pod。

OOMKiller 是一种最后的手段机制，仅在系统面临内存不足的危险时才调用。虽然它可以帮助防止系统因内存耗尽而崩溃，但重要的是要注意，终止进程可能导致数据丢失和系统不稳定。因此，`建议配置系统以避免 OOM 情况`，例如，通过监视内存使用情况、设置资源限制和优化应用程序中的内存使用情况。

可以通过调整内核参数来修改 ，OOM 是否自动触发。如果内核参数`sysctl vm.panic_on_oom`设置为1而不是0，内核将会发生 panic,即直接摆烂，什么时候挂掉算什么时候。默认为0.即自动启动OOM killer

```bash
┌──[root@liruilongs.github.io]-[~]
└─$ sysctl vm.panic_on_oom
vm.panic_on_oom = 0
```

在后台，Linux 内核为主机上运行的每个进程保持一个运行不良评分。此分数越高，进程被终止的可能性就越大。

```bash
[root@ecs-liruilong ~]# cat /proc/1/oom_score
0
```

分数越高，进程越有可能被OOM杀手杀死。许多因素被用来计算这个分数:

- VM大小(不是RSS大小)，
- 进程所有子进程的累积VM大小，
- nice值(正的nice值会给出更高的分数)，
- 总运行时间(较长的总运行时间会降低分数)，
- 运行用户(根进程会得到轻微的保护)，
- 进程执行直接硬件访问，分数也会降低。
- 内核本身和PID1 (sysemd)是免疫的OOM杀手。

**如果你希望强制的执行`OOM Killer`**

可以`echo f > /proc/sysrq-trigger`，但请记住，至少会有一个进程被杀死。

```bash
[root@ecs-liruilong ~]# echo f > /proc/sysrq-trigger

Message from syslogd@ecs-liruilong at Aug  1 14:32:18 ...
 kernel:[340648.118967] Kernel panic - not syncing: Out of memory: system-wide panic_on_oom is enabled
```

输出将被发送到dmesg。

```bash
[root@ecs-liruilong ~]# cat /var/log/dmesg
```

可调的`/proc/PID/oom_adj`可以用来手动调整`oom_score`。配置该pid进程被oom killer杀掉的`权重`，`oom_adj`可以的值从`-17到15`，其中0表示不改变(默认)，越高的权重，意味着更可能被[oom killer](https://zhida.zhihu.com/search?content_id=232440962&content_type=Article&match_order=2&q=oom+killer&zhida_source=entity)选中，-17表示免疫(永远不会杀死)。

```bash
[root@ecs-liruilong ~]# cat /proc/1/oom_adj
0
```

Kubernetes 在为 Pod 定义服务质量 (QoS) 类时使用该值。有三个 QoS 类可以分配给一个 pod，每个类都有一个匹配的值：`oom_score_adj`

- `Guaranteed`(完全可靠的): -997
- `BestEffort`: 1000
- `Burstable`: `min(max(2, 1000 — (1000 * memoryRequestBytes) / machineMemoryCapacityBytes), 999)`

当前的 QoS 级别 直接由 Requests 和 Limits 来定义。在Kubernetes中容器的QoS级别等于容器所在Pod的QoS级别

要查看 Pod 的 QoS 类，请运行以下命令：

```bash
┌──[root@vms100.liruilongs.github.io]-[~/ansible/oomkiller]
└─$kubectl get pod oom-killer-pod -o jsonpath='{.status.qosClass}'
Burstable┌
```

如果Pod中的所有容器对所有资源类型都定义了 `Limits`和`Requests`，并且所有容器的Limits值都和Requests值全部相等(且都不为0)，那么该Pod的QoS级别就是`Guaranteed`

`Guaranteed` 级别的 Pod 是 在节点过载时最后被 Kill 掉的 Pod,所以如果当前 Pod 很重要，建议设置为 `Guaranteed` 级别，可以减少当前节点其他 Pod 超用的影响，或者考虑 HPA

### OOMKilled诊断

1. 检查 Pod 日志：诊断 OOMKilled [错误的第一步](https://zhida.zhihu.com/search?content_id=232440962&content_type=Article&match_order=1&q=错误的第一步&zhida_source=entity)是检查 Pod 日志，以查看是否有任何指示内存问题的错误消息。描述命令的事件部分将提供进一步的确认和错误发生的时间/日期。

```bash
kubectl describe pod <podname>

State:          Running
       Started:      Fri, 12 May 2023 11:14:13 +0200
       Last State:   Terminated
       Reason:       OOMKilled
       Exit Code:    137
       ...
```

可以查询 Pod对应 宿主机 `cat /var/log/pods/<podname>`日志：

```bash
┌──[root@vms100.liruilongs.github.io]-[/var/log/pods]
└─$ls
default_release-name-prometheus-node-exporter-gbnmx_5d6e840f-ff49-4211-a705-7c2579783eb4
kube-system_calico-node-rkxps_10cf4581-1c4e-4c30-a621-45d43ab867d5
kube-system_coredns-c676cc86f-kpvcj_bb27b0db-02e1-405d-bd17-9f2178c1e378
kube-system_coredns-c676cc86f-xqj8d_22fab2bd-5df6-42a5-b251-f112d2d798f9
kube-system_etcd-vms100.liruilongs.github.io_e8c17bb99f9bd8119cdd769556041e18
kube-system_haproxy-vms100.liruilongs.github.io_440fdce2882de0bd97840ce699681bd2
kube-system_keepalived-vms100.liruilongs.github.io_c73a6f974cefc7114a28dfb29c95c8b1
kube-system_kube-apiserver-vms100.liruilongs.github.io_ddd138ad87313d896ee03a878d01ee67
kube-system_kube-controller-manager-vms100.liruilongs.github.io_987f1cce63f2d4e44db9398fe35d309b
kube-system_kube-proxy-fqck4_b0ed853d-3b71-4a2c-b95c-eee0a0c29bce
kube-system_kube-scheduler-vms100.liruilongs.github.io_d819bb5a46d9f3c35e9594adcb22be80
metallb-system_speaker-4tdl6_4ab28c7d-1d5d-458e-8dfb-d3b8439f7f78
┌──[root@vms100.liruilongs.github.io]-[/var/log/pods]
└─$pwd
/var/log/pods
```

1. 监控内存使用情况：使用 Prometheus 或 Grafana 等 Kubernetes 监控工具监控 Pod 和容器中的内存使用情况。这可以帮助您确定哪些容器消耗了太多内存并触发了 OOMKilled 错误。
2. 使用内存性能分析器：使用 pprof 等内存性能分析器来识别可能导致内存过度使用的[内存泄漏](https://zhida.zhihu.com/search?content_id=232440962&content_type=Article&match_order=1&q=内存泄漏&zhida_source=entity)或低效代码。

### OOMKilled K8s 错误和解决的常见原因

已达到容器内存限制。这可能是由于在容器清单中指定的`内存限制值上设置了不适当的值`，这是允许容器使用的最大内存量。这也可能是由于应用程序遇到比正常情况更高的负载。解决方法是`增加内存限制的值`，或调查负载增加的根本原因并进行修正。造成这种情况的常见原因包括上传大文件，因为上传大文件会消耗大量内存资源，尤其是当多个容器在一个 Pod 中运行时，以及流量突然增加带来的高流量。

已达到容器内存限制，因为应用程序遇到`内存泄漏`。需要调试应用程序以`解决内存泄漏的原因`。

`节点过载` — 这意味着 Pod 使用的总内存大于可用的总节点内存。通过纵向扩展来增加节点的可用内存，或将 Pod 移动到具有更多可用内存的节点。您还可以调整在过度使用的节点上运行的 Pod 的内存限制，以便它们适合可用边界，请注意，您还应该注意内存请求设置，该设置指定了 Pod 应使用的最小内存量。如果此值设置得太高，则可能无法有效使用可用内存。

在调整内存请求和限制时，请记住，当节点过载时，Kubernetes 会根据(Qos 等级)以下优先级顺序杀死 Pod：

- 没有请求或限制的 Pod
- 有请求但没有限制的 Pod
- 使用 的 Pod 超过其内存请求值(指定的最小内存)，但低于其内存限制
- 使用超过其内存限制的 Pod

### 要点

为避免 OOMKilled 错误，建议监控 Kubernetes Pod 和容器中的内存使用情况，设置资源限制以防止容器消耗过多内存，并优化应程序代码以减少内存消耗。

此外，请考虑增加分配给 Pod 的内存资源，或使用水平 Pod 自动缩放来扩展 Pod 数量，以响应增加的工作负载需求。