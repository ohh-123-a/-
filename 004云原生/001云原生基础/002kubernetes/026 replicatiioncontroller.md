rc

```yaml
# 版本
apiVersion: v1
# 类型
kind: ReplicationController
# 数据
metadaate:
	name: nginx0
# 信息
spec:
	# pod的数量
	replicas: 3
	# 信息标签
	selector:
		app: nginx0
	# 模版
	template:
		# pad的元数据
		metadata:
			# pod的名称
			name: nginx0
			# pod的标签
			labels:
				app: nginx0
		# pod详细信息					
		spec:
			# 重启策略
			restart: OnFailure
			# 容器列表
			containers:
			# 容器1的描述
			- name: nginx0
			  image: docker.io/library/nginx:latest
			  imagePullPolicy: Never
			  ports:
			  - name: nginxport
			    containerPort: 80
		
```

```shell
# 创建rc
kubectl create -f test0008.yaml
# 删除rc
kubectl delete -f test0008.yaml
# 创建rc
kubectl create -f test0008.yaml
# 删除rc
kubectl delete replicationcontroller nginx0
```

