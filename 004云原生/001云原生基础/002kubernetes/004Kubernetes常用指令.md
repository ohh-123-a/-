# 一、Kubernetes 自动补齐

```shell
[root@master ~]# yum -y install bash-completion		# 安装自动补齐软件
Repository appstream is listed more than once in the configuration
上次元数据过期检查：2:28:41 前，执行于 2023年12月21日 星期四 18时27分29秒。
软件包 bash-completion-1:2.7-5.el8.noarch 已安装。
依赖关系解决。
无需任何处理。
完毕！
[root@master ~]# source <(kubectl completion bash)		# 临时开启自动补齐功能
[root@master ~]# echo "source <(kubectl completion bash)" >> ~/.bashrc		# 永久开启自动补齐功能
etcdctl --endpoints 127.0.0.1:2379 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key member list
etcdctl  --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/peer.crt --key=/etc/kubernetes/pki/etcd/peer.key member remove f32b535cea1c59f4
```

# 二、Kubernetes 基础命令

**kubectl + [command] + [type] + [name]+ [flags]**

command：指定要对一个或多个资源执行的操作，例如create、get、describe、delete等。(增删改查)

type：指定资源类型。资源类型不区分大小写，可以指定单数、复数或缩写形式。

name：指定资源的名称。名称区分大小写。如果省略名称，则显示所有资源的详细信息： kubectl get pods。

flags: 指定可选的参数。例如，可以使用-s或-server参数指定 Kubernetes API服务器的地址和端口。

## 1. 初级基础命令

### kubectl create

通过 yaml/json 文件或者标准输入创建一个资源对象

**常用子命令及参数（按两次 tab 键即可查到）：**

- clusterrolebinding   (为特定群集角色创建群集角色绑定)
- clusterrole          	 (创建群集角色)
- configmap           	 (从本地文件、目录或文字值创建配置映射)
- cronjob              	 (使用指定的名称创建cron作业)
- deployment          	 (使用指定的名称创建资源)
- job                  		 (创建具有指定名称的作业)
- namespace           	 (用指定的名称创建一个命名空间)
- priorityclass       	 (使用指定的名称创建优先级类)
- rolebinding        	 (为特定角色或群集角色创建角色绑定)
- role                 		 (使用单一规则创建角色)
- secret              	 (使用指定的子命令创建机密）
- serviceaccount         (创建一个指定名称的服务账户)
- service              	 (使用指定的子命令创建服务)
- token               	 (请求服务帐户令牌)
- -f				 (指定某个 yaml 或 json 文件来创建资源)

------

### kubectl expose

将 json/yaml 文件中定义的资源对象的端口暴露给新的 service 资源对象

**常用子命令及参数：**

- deployment        		(管理Pod和ReplicaSet的声明式更新的一种资源对象)  
- replicaset             		(副本控制器。控制由其管理的Pod，使Pod副本的数量始终维持在预设的个数)
- service                		(暴漏服务的端口)
- pod                    		(暴露 Pod 容器组的端口)
- replicationcontroller  	(检查 Pod 健康状态的控制器)

------

### kubectl run

创建并允许一个或多个容器镜像

**常用子命令及参数：**

- --image		(指定某个镜像)

------

### kubectl set

配置资源对象设置特定功能

**常用子命令及参数：**

- env            		 (更新 Pod 模板上的环境变量)
- image           		 (更新 Pod 模板上的镜像)
- resources      		 (使用 Pod 模板更新对象的资源请求/限制)
- selector        		 (为资源设置选择器)
- serviceaccount  	 (更新资源的服务帐户)
- subject        		 (更新角色绑定或群集角色绑定中的用户、组或服务帐户)

------

## 2. 中级基础命令

### kubectl explain

查看资源对象的详细信息，一般用于编写 yaml 的时候做一个提示。

比如：kubectl explain deployment 会出现 deployment 下面可以写的字段，以及字段属性并且可以逐级使用。

------

### kubectl get

获取一个或多个资源对象的信息，如：pod 的运行状态、容器数量、命名空间等。

**常用子命令及参数：**

- -n, --namespace		(指定要查询的命名空间。如果不指定，默认查询当前命名空间中的资源)
- -o, --output			(指定输出格式。可以指定yaml、json、wide等格式。例如，使用-o yaml可以以yaml格式查看pod的信息，也可以指定已有资源加-o wide 查询状态信息)
- -w, --watch			(实时观察资源的变化)
- --all-namespaces,-A	(获取所有命名空间中的资源信息)
- -l, --selector			(根据标签选择器过滤资源。可以使用等于、不等于、包含、不包含等操作符来定义选择器条件)
- --field-selector		(根据字段选择器过滤资源)
- --sort-by				(按照指定的字段对结果进行排序)
- --show-kind			(显示资源的种类信息)
- --watch-only			(只观察资源的变化，不输出当前资源信息)
- pod					(查询当前的 pod 资源，不加其他参数表示查询默认命名空间下的 pod)
- nodes				(查询当前 k8s 集群的所有节点及状态)

------

### kubectl edit

使用默认编辑器编辑服务器上定义的资源对象，保存后自动应用更改到 kubernetes 集群配置中。

**常用子命令及参数：**

- -f, --filename			(指定要编辑的资源对象的文件或URL)
- -patch				(将文件中的更改作为补丁应用于资源对象)
- -p, --patch-strategy	(指定补丁策略。可以指定merge、retainKeys或strategic-merge)
- --output-patch		(输出补丁文件)
- --local				(在本地进行编辑，而不是在集群中进行编辑)
- --edit-cache			(编辑缓存中的资源对象)

------

### kubectl delete

通过 json/yaml 文件、标准输入、资源名称或标签选择器来删除资源

**常用子命令及参数：**

- -f, --filename			(指定要删除的资源对象的文件或URL)
- --force				(强制删除资源，即使资源正在运行)
- --cascade			(删除与资源相关联的所有子资源)
- --all					(删除所有资源。**慎用！！**)
- --grace-period		(设置宽限期)
- -n					(指定命名空间)

------

## 3. 部署命令

### kubectl rollout

资源管理对象的部署

**常用子命令及参数：**

- history  			(显示上线历史)                
- resume  		 	(恢复暂停的资源)
- pause    			(将所指定的资源标记为已暂停)  
- status   			(显示上线的状态)
- restart  			(重启一个资源)         
- undo     			(撤销上一次的上线)

------

### kubectl rollout-update

使用 rc（replication controller）来做滚动更新资源，包括 Deployment、StatefulSet、Job 等。

------

### kubectl scale

对资源扩容或着缩容，包括 deployment、replicaset、replication、controller 等。

**常用子命令及参数：**

- deployment			(扩展或缩减 Deployment 控制的 Pod 数量)
- replicaset				(扩展或缩减 Replicaset 控制的 Pod 数量)
- --replicas            		(控制 Pod 数量)
- replicationcontroller	(控制 Pod 数量需要达到相对值的参数)
- statefulset			(部署和扩展有状态的应用程序)

------

## 4. 集群管理命令

### kubectl cetificate

修改证书资源对象

**常用子命令及参数：**

- approve			(批准证书签名请求(CSR))
- reject			(拒绝 CSR)
- get				(获取集群中存在的证书及其相关信息)
- describe			(查看集群中证书的详细信息，包括颁发者、有效期、主题、公钥等)
- sertificates		(获取一个证书的私钥)

------

### kubectl cluster-info 

查看集群的概要信息

**常用子命令及参数：**

- dump		(获取集群详细信息，并可通过 grep 等过滤命令对集群进行问题诊断、调试测试等)

------

### kubectl top

显示资源、CPU、内存、储存的使用情况

**常用子命令及参数：**

- node		(显示集群的资源使用情况)
- pod			(显示 pod 的资源使用情况，不加其他参数显示的是默认 pod)

------

### kubectl cordon

标记集群内的某个节点为不可调度负载节点

**常用子命令及参数：**

- 节点名		(如：master、node01 等)

------

### kubectl uncordon

指定某个节点为可调度节点

**常用子命令及参数：**

- 节点名		(如：master、node01 等)

------

### kubectl drain

将某个节点上的工作负载平滑地切换到其他节点

**常用子命令及参数：**

- 节点名		(如：master、node01 等)

------

### kubectl taint

将一个或多个节点设置为污点，具有污点的节点和 Pod 之间存在一种互斥关系，这可以让节点拒绝 Pod 的调度执行，甚至将已经存在的 Pod 驱逐出去。

**常用子命令及参数：**

- node+节点名+节点名....

------

## 5. 故障排查和调试命令

### kubectl describe

显示一个或多个资源对象的详细信息

**常用子命令及参数：**

- nodes				(显示某个节点的详细信息)
- pod					(显示某个 pod 的详细信息)
- -f .yaml/json			(显示某个 yaml/json 文件定义的资源的详细信息)
- -l					(指定显示的资源对象)
- --include-uninitialized	   (显示未初始化的资源对象)

------

### kubectl logs

输出 pod 资源对象中一个容器的日志

**常用子命令及参数：**                        

- replicasets         	(显示 pod 数量控制器的日志)    
- services			(显示 services 日志)
- deployments           (显示 deployments 资源的日志)
- pods                    	(显示 pod 的日志)

------

### kubectl attach

连接到一个运行的容器

**常用子命令及参数：**

- -c			(指定要进入的某个容器)
- -i			(将控制台输入发送到容器)
- -t			(为容器分配一个伪终端)

------

### kubectl exec

在指定容器内执行命令，也可连接到某个容器

**常用子命令及参数：**

- -c			(指定要进入的某个容器)
- -i			(将控制台输入发送到容器)
- -t			(为容器分配一个伪终端)
- -f			(通过已定义好的 yaml/json 文件对容器进行交互)

------

### kubectl port-forward

将本机指定端口映射到 pod 资源对象的端口

**常用子命令及参数：**

- Pod			(要转发端口的 Pod 名称或选择器,例如：nginx 8080：80，这里 nginx 为 Pod 名)

------

### kubectl proxy

将本地指定端口映射到 API Server

**常用子命令及参数：**

- -port=PORT			(代理服务的端口号。默认为 8001)
- -www=static-dir		(将静态文件目录提供给代理服务)
- -www-prefix=prefix	(将 static-dir 中的文件路径前缀添加到代理服务中)
- -api-prefix=prefix		(将 API 路径前缀添加到代理服务中)

------

### kubectl cp

用于 pod 与主机交换文件

**常用子命令及参数：**

- -c			(指定容器名进行文件交互)

------

### kubectl auth

检查操作是否被允许，例如检查是否可以在当前名称空间中列出部署、执行所有操作等。

**常用子命令及参数：**

- can-i      		(检查是否允许操作)
- reconcile  	(协调RBAC角色、角色绑定、集群角色和集群角色绑定对象的规则…)
- whoami    	(显示当前认证用户)

------

## 6. 高级命令

### kubectl diff

对比本地 json/yaml 文件与 kube-apiserver 中运行的配置文件是否有差异

**常用子命令及参数：**

- -f		(指定要比较的资源对象的文件名或路径)
- -o		(指定输出格式，例如“yaml”或“json”)

------

### kubectl apply 

通过 json/yaml 文件、标准输入对资源进行配置更新或创建

**常用子命令及参数：**

- -f			(指定要应用的资源对象的文件名或路径，可以是YAML、JSON或多个文件)
- -n 			(指定要应用的资源对象的命名空间)
- --recursive	(递归地应用目录中的所有资源对象)
- --prune		(删除不再存在的资源对象)
- --force		(强制应用资源对象，即使它们已存在)

------

### kubectl patch

通过 patch 方式修改资源对象字段（补丁式）

**常用子命令及参数：**

- -f			（指定要修改的资源对象的文件名或路径）
- -p			（指定要应用的补丁，可以是JSON或YAML格式）
- --type		（指定资源对象的类型，例如“Pod”、“Deployment”等）
- -n 			（指定要修改的资源对象的命名空间）

------

### kubectl replace

通过 json/yaml 文件或标准输入来替换资源对象

**常用子命令及参数：**

- -f			(指定要替换的资源对象的文件名或路径)
- --force		(强制应用资源对象，即使它们已存在)

------

### kubectl wait

在一个或多个资源上等待条件达成

**常用子命令及参数：**

- -f				(指定要等待的资源对象的文件名或路径)
- --for=条件		(指定要等待的条件，例如“Ready=True”)
- --timeout=时间	(设置等待的超时时间)

------

### kubectl convert

转换 json/yaml 文件为不同的资源版本

**常用子命令及参数：**

- -f				(指定 yaml/json 文件)
- --v1				(将资源对象转换为 v1 版本)
- --v1beta1		(将资源对象转换为 v1 版本测试版 1)

------

### kubectl kustomize 

定制 kubernetes 配置

**常用子命令及参数：**

- -f				(指定要应用的Kustomize配置文件的文件名或路径)
- -n				(指定要应用的资源对象的命名空间)

------

## 7. 设置命令

### kubectl label 

增删改资源的标签

**常用子命令及参数：**

- -f				(指定要修改标签的资源对象的文件名或路径)
- 标签名=新标签名	(添加一个标签到资源对象上)
- --overwrite		(如果资源对象已经存在标签，则覆盖该标签)
- 标签名-			(删除一个标签)

------

### kubectl annotate

更新⼀个或者多个资源对象的注释（annotaion）信息

**常用子命令及参数：**

- -f							(指定 yaml/json 文件)
- --overwrite					(如果资源对象已经存在注释，则覆盖已有注释)
- KEY_1=VAL_1 ... KEY_N=VAL_N	(指定要添加或更新的注释键值对)

------

### kubectl completion

开启 kubernetes 命令自动补齐功能。

------

## 8. 其他命令

- **kubectl config** 		管理 kubeconfig 配置文件
- **kubectl plugin** 		运行命令行插件功能
- **kubectl version** 		查看客户端服务端的系统版本信息
- **kubectl api-versions** 	列出当前 kubernetes 系统支持的资源组和资源版本表现形式为/
- **kubectl api-resources** 	列出当前 kubernetes 系统支持的resource资源列表
- **kubectloptions** 		查看支持的参数列表

------

# 三、kubernetes 常用命令

## 1. 编写 yaml 文件

```shell
[root@master ~]# mkdir k8s
[root@master ~]# cd k8s
[root@master k8s]# vim busybox.yaml
apiVersion: v1
kind: Pod
metadata:
        name: busybox-sleep
spec:
        containers:
        - name: busybox
          image: busybox:1.28
          args:
          - sleep
          - "1000"
```

------

## 2. kubectl create

通过配置文件名或标准输入创建一个集群资源对象，支持 json 和 yaml 格式的文件

**语法：**

kubectl create -f 文件名

```shell
[root@master k8s]# kubectl create -f ./busybox.yaml
pod/busybox-sleep created
# 通过json或yaml文件创建pod
[root@master k8s]# kubectl get pod
NAME            READY   STATUS    RESTARTS   AGE
busybox-sleep   1/1     Running   0          81s
[root@master k8s]# cat busybox.yaml | kubectl create -f -
pod/busybox-sleep created
# 通过标准输入来创建pod
[root@master k8s]# kubectl get pod
NAME            READY   STATUS    RESTARTS   AGE
busybox-sleep   1/1     Running   0          12s
```



kubectl create deployment 资源名 --image=镜像名

```shell
[root@master k8s]# kubectl create deployment nginx --image=nginx:latest
deployment.apps/nginx created
# 通过指定镜像创建pod
[root@master k8s]# kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
busybox-sleep            1/1     Running   0          2m52s
nginx-56fcf95486-tj26q   1/1     Running   0          43s
```



kubectl create deployment 资源名 --image=镜像名 --dry-run=client -o yaml > 文件名.yaml

```shell
[root@master k8s]# kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx.yaml
# 不创建pod只生成yaml文件
[root@master k8s]# ls
busybox.yaml  nginx.yaml
[root@master k8s]# cat nginx.yaml 		# 生成的nginx.yaml文件
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

------

## 3. kubectl get

**语法：**

kubectl get resource_name(资源名、类型)

**常见 resource_name：**

| **子命令**                 | **解释**                                                     |
| -------------------------- | ------------------------------------------------------------ |
| all                        | 获取 kubernetes 集群下所有资源，一般显示 kubernetes 自身的服务 |
| certificatesigningrequests | (缩写 csr) 获取集群中的证书签名请求                          |
| clusterrolebindings        | 获取控制 API Server 访问的绑定角色                           |
| clusterrol                 | 获取集群级别的权限操作                                       |
| componentstatuses          | (缩写 cs) 查看 master 及组件状态                             |
| configmaps                 | (缩写 cm) 查看存储非机密数据的 kubernetes 资源，如配置信息等 |
| controllerrevisions        | 查看用于跟踪 controller 历史版本的资源                       |
| cronjobs                   | 查看 kubernetes 集群的周期性任务                             |
| customresourcedefinition   | (缩写 crd) 查看集群中所有 CRD 的列表，包括它们的名称、API 版本、类型、标签和描述等信息 |
| daemonsets                 | (缩写 ds) 查看 kubernetes 守护进程状态                       |
| deployments                | (缩写 deploy) 获取 Kubernetes 集群中所有的 Deployment 资源，用于声明式地管理应用程序的副本数量和配置的资源 |
| endpoints                  | (缩写 ep) 获取 Kubernetes 服务的端点，包含了访问服务的 IP 地址和端口信息 |
| events                     | (缩写 ev) 获取集群中默认命名空间的相关操作的事件列表，包括增删改查等 |
| horizontalpodautoscalers   | (缩写 hpa) 获取集群中所有的 HorizontalPodAutoscaler 资源：用于自动调整 Pod 的副本数量以响应集群中的资源使用情况 |
| ingresses                  | (缩写 ing) 获取集群中所有的 Ingress 资源：用于定义如何将外部流量路由到集群内部的应用程序 |
| jobs                       | 查看用于执行批处理任务或短生命周期的任务                     |
| limitranges                | (缩写 limits) 获取集群中所有的 LimitRange 资源：用于定义 Pod 的资源限制范围 |
| namespaces                 | (缩写 ns) 获取集群中已有的命名空间列表                       |
| networkpolicies            | (缩写 netpol) 获取定义网络流量访问规则的资源                 |
| nodes                      | (缩写 no) 查看集群中所有节点                                 |
| persistentvolumeclaims     | (缩写 pvc) 查看集群中用于申请持久卷的资源                    |
| persistentvolumes          | (缩写 pv) 查看集群中用于数据持久化的存储卷                   |
| poddisruptionbudgets       | (缩写 pdb) 获取该资源以查看限制集群中某个 Pod 集合并发的删除操作 |
| podpreset                  | 获取用于在创建 Pod 时注入其他运行时需要的信息，这些信息包括 secrets、volume mounts、environment variables 等 |
| pods                       | (缩写 po) 获取 pod 资源信息，不加参数获取的是当前默认命名空间的资源 |
| podsecuritypolicies        | (缩写 psp) 查看用于控制 Pod 的安全相关配置                   |
| podtemplates               | 查看用于定义 pod 模板的资源对象                              |
| replicasets                | (缩写 rs) 查看目前所有的replica set，显示了所有的pod的副本数，以及他们的可用数量以及状态等信息 |
| replicationcontrollers     | (缩写 rc) 列出所有 ReplicationControllers 的命令，ReplicationControllers 负责确保特定数量的 Pods 始终在运行 |
| resourcequotas             | (缩写 quota) 列出所有 ResourceQuotas，ResourceQuota 是一个资源限制对象，用于限制命名空间中资源的使用量 |
| rolebindings               | RoleBinding 通过已经定义的 Role 权限授予到用户、用户组，从而让用户获得在 NameSpace 对应的操作资源权限 |
| roles                      | 列出用于限制资源访问的权限对象                               |
| secrets                    | 列出 Secrets 用于存储敏感信息，例如密码、令牌或 SSH 密钥     |
| serviceaccounts            | (缩写 sa) 显示 service 的敏感信息数量及年龄(服务生成时间)    |
| services                   | (缩写 svc) 列出所有 services 的名称、类型、IP、端口、年龄    |
| statefulsets               | (缩写 sts) 列出所有可用 StatefulSet 对象，包括其名称、状态和任何相关的标签和注释 |
| storageclasses             | (缩写 sc) 列出所有可用的 StorageClass 对象，包括其名称、提供程序、回收策略、卷绑定模式、是否允许卷扩展等信息 |

**其他可选参数：**

- -o wide/json/yaml		(用不同格式查看)
- -l key=value			(看指定标签的pods，⽀持‘=’, ‘==’, and ‘!=’操作符)

**示例：**

```shell
[root@master k8s]# kubectl get componentstatuses
# 查看Master状态
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE   ERROR
controller-manager   Healthy   ok        
scheduler            Healthy   ok        
etcd-0               Healthy   ok        

[root@master k8s]# kubectl get namespaces 
# 查看所有命名空间
NAME                   STATUS   AGE
default                Active   2d
kube-node-lease        Active   2d
kube-public            Active   2d
kube-system            Active   2d
kubernetes-dashboard   Active   40h

[root@master k8s]# kubectl get pods -n kube-system 
# 列出kube-system命名空间下的所有pod，不加-n参数查看default命名空间下的pod
NAME                                       READY   STATUS    RESTARTS         AGE
calico-kube-controllers-6d48795585-4nt8s   1/1     Running   3 (6h13m ago)    47h
calico-node-4vxf7                          1/1     Running   3 (6h13m ago)    47h
calico-node-7hxmr                          1/1     Running   2 (6h13m ago)    47h
calico-node-lwktw                          1/1     Running   2 (6h13m ago)    47h
coredns-6554b8b87f-jbx9s                   1/1     Running   3 (6h13m ago)    2d
coredns-6554b8b87f-vfrs7                   1/1     Running   3 (6h13m ago)    2d
etcd-master                                1/1     Running   10 (6h13m ago)   2d
kube-apiserver-master                      1/1     Running   16 (6h13m ago)   2d
kube-controller-manager-master             1/1     Running   12 (6h12m ago)   2d
kube-proxy-8nzdl                           1/1     Running   3 (6h13m ago)    40h
kube-proxy-t4vzv                           1/1     Running   2 (6h13m ago)    40h
kube-proxy-xsbw6                           1/1     Running   2 (6h13m ago)    40h
kube-scheduler-master                      1/1     Running   9 (6h13m ago)    2d
metrics-server-8df99c47f-gvljv             1/1     Running   3 (6h12m ago)    28h

[root@master k8s]# kubectl get pods -o wide 
# 显示更多的pods列表信息(例如 pod的ip和所处的node)
NAME                     READY   STATUS    RESTARTS   AGE   IP              NODE         NOMINATED NODE   READINESS GATES
nginx-7854ff8877-nphcf   1/1     Running   0          14s   172.16.58.198   k8s-node02   <none>           <none>

[root@master k8s]# kubectl get replicationcontroller web
# 列出名字为web的rc

[root@master k8s]# kubectl get -o json pod nginx-7854ff8877-nphcf
# 获取名字为nginx-7854ff8877-nphcf的pod的信息，并以json格式输出
{
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
        "annotations": {
            "cni.projectcalico.org/containerID": "1fa379d56d4bd8fd0687608bd7631b1f5b212d959308fed397e3e13a52f16a50",
            "cni.projectcalico.org/podIP": "172.16.58.198/32",
            "cni.projectcalico.org/podIPs": "172.16.58.198/32"
        },
......省略部分内容......

[root@master k8s]# kubectl get -f busybox.yaml -o json
# 根据pod⽂件查找pod，并以json格式输出，查询前确保目标pod存在于集群中
{
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
        "annotations": {
            "cni.projectcalico.org/containerID": "b0399bc00863434eb1bcf99f0b01b16f8458ff6eb5e01101c4d8a85f540edc0a",
            "cni.projectcalico.org/podIP": "172.16.58.199/32",
            "cni.projectcalico.org/podIPs": "172.16.58.199/32"
        },
        "creationTimestamp": "2023-12-23T06:57:45Z",
        "name": "busybox-sleep",
        "namespace": "default",
        "resourceVersion": "150996",
        "uid": "e4cad329-4969-438a-a22a-61b059430a67"
    },
......省略部分内容......

[root@master k8s]# kubectl get -o template pod/kube-dns-795f5f6f9c-ldxxs --template {{.status.phase}}
# 获取pod的kube-dns-795f5f6f9c-ldxxs容器的状态

[root@master k8s]# kubectl get rc,services
# 同时获取所有的rc和service
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d

[root@master k8s]# kubectl get rc/web service/frontend pods/web-pod-13je7
# 获取符合条件的所有rc,svc,pod，因为当前集群中没有这些资源，所以查不到便报错
Error from server (NotFound): replicationcontrollers "web" not found
Error from server (NotFound): services "frontend" not found
Error from server (NotFound): pods "web-pod-13je7" not found

[root@master k8s]# kubectl get all
# 显示所有资源，包括pod、deployment、service、replicaset
NAME                         READY   STATUS    RESTARTS   AGE
pod/busybox-sleep            1/1     Running   0          4m16s
pod/nginx-7854ff8877-nphcf   1/1     Running   0          8m6s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           8m6s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7854ff8877   1         1         1       8m6s

[root@master k8s]# kubectl get pods nginx-7854ff8877-nphcf -o jsonpath='{.spec.containers[*].name}'
# 查看pod⾥的容器名
nginx
```

------

## 4. kubectl delete

kubernetes 集群中，可通过指定配置文件（json/yaml）、stdin（标准输入）、资源名称或 label（标签）来删除资源。

Pod 支持平滑删除，因为这些资源一般是集群中的实体，所以删除无法立即生效，这些资源在强制终止之前默认定义了一个周期 (宽限期)，但可以使用“-grace-period flag”来覆盖该值，或通过“pass --now”设置该周期为 1.

如果托管 Pod 的 Node 节点已停止或无法连接 API Server，使用 delete 删除 Pod 需等待时间更长，此时要想强制删除，需指定“-force flag”，且设置周期为 0。

- **注意：**执行delete命令时不会检查资源版本，如果在执行delete操作时有人进行了更新操作，那么更新操作将连同资源一起被删除。

**语法：**

kubectl delete ([ -f 文件名 ] | TYPE [NAME | -l label | --all])

**示例：**

```shell
# 使⽤ pod.json中指定的资源类型和名称删除pod
kubectl delete -f ./pod.json

# 根据传⼊stdin的JSON所指定的类型和名称删除pod
cat pod.json | kubectl delete -f -

# 删除名为“baz”和“foo”的Pod和Service
kubectl delete pod,service baz foo

# 删除 Label name = myLabel的pod和Service
kubectl delete pods,services -l name=myLabel

# 强制删除dead node上的pod
kubectl delete pod foo --grace-period=0 --force

# 删除所有pod
kubectl delete pods --all

# 删除⼀个部署集
kubectl delete deployments nginx
```

------

## 5. kubectl set

**更新资源：**

```shell
# 更新 "nginx" Deployment 的 "nginx" 容器镜像
kubectl set image deployment nginx nginx=nginx:v2
```

------

## 6. kubectl edit

**编辑资源：**

```shell
# 编辑名为nginx的集群管理器，进入一个如vim的文本编辑器，但不能使用鼠标滚轮滑动
kubectl edit deployment nginx
```

------

## 7. kubectl replace

**更新资源：**

```shell
# 强制替换，删除后重建资源。会导致服务不可⽤。
kubectl replace --force -f ./nginx.yaml
```

------

## 8. kubectl logs

输出 pod 中一个容器的日志。如果 pod 只包含一个容器则省略容器名。

**语法：**

kubectl logs [-f] [-p] POD [-c CONTAINER]

- -c,--container=""：容器名。
- -f,--follow[=false]：指定是否持续输出日志
- --interactive[=true]：如果为true，当需要时提示用户进行输入。默认为true。
- --limit-bytes=0：输出日志的最大字节数。默认无限制。
- -p,--previous[=false]：如果为true，输出pod中曾经运行过，但目前已终止的容器的日志
- --since=0: 仅返回相对时间范围，如5s、2m或3h，之内的日志。默认返回所有日志。只能司时使用since和since-time中的一种。
- --since-time="”：仅返回指定时间 (RFC3339格式)之后的日志。默认返回所有日志。只能同时使用since和since-time中的一种。
- --tail=-1: 要显示的最新的日志条数。默认为-1，显示所有的日志。
- --timestamps[=false]: 在日志中包含时间戳。

**示例：**

```shell
# 返回仅包含⼀个容器的pod nginx的⽇志快照
kubectl logs nginx

# 持续输出pod⽇志
kubectl logs -f nginx-7854ff8877-x8v6f

# 指定命名空间和pod查看⽇志
kubectl logs -f kube-apiserver-k8s-master -n kube-system

# 查看后⾯10⾏内容
kubectl logs -f kube-apiserver-k8s-master -n kube-system --tail 10

# 查看某个pod⾥某个容器的⽇志
kubectl logs -f nginx-7854ff8877-x8v6f -c nginx
```

------

## 9. kubectl exec

连接容器并可在容器内部执行命令

**语法：**

kubectl exec POD [-c CONTAINER] -- COMMAND [args...]

- -c,--container="”: 容器名。如果未指定，使用pod中的一个容器。
- -p,--pod="”: Pod名
- -i,--stdin[=false]: 将控制台输入发送到容器
- -t,--tty[=false]: 为容器分配一个伪终端

**示例：**

```shell
# 在pod的容器⾥执⾏ls指令
kubectl exec busybox-sleep -- ls

# 进⼊到pod容器⾥
kubectl exec -it busybox-sleep -- bash
kubectl exec -it busybox-sleep -- sh

# 进⼊到某个pod⾥的某个容器⾥
kubectl exec -it nginx-7854ff8877-x8v6f -c nginx -- bash
```

------

## 10. kubectl top metrics-server

显示资源 cpu 内存 存储使⽤情况

```shell
# 查看节点的资源使⽤情况
kubectl top node

# 查看pod资源使⽤情况
kubectl top po

# 查看所有节点资源使⽤情况
kubectl top po -A
```