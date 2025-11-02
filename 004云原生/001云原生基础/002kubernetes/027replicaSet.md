rs 是rc的升级，更加灵活，方便项目升级

```yaml
apiVersion: app/v1
kind: ReplicaSet
metadata:
	name: nginx
spec:
	replicas: 4
	selector:
		matchLabels:
			tier: nginx
		matchExpressions:
			- key: tier
			  operator: In
			  values:
			  	- nginx
	template:
		metadata:
			name: nginx
			labels:
				tier: nginx
				app: guestbook
		spec:
			containers:
				- name: nginx
				  image: docker.io/library/nginx:latest
				  imagePullPolicy: Never
				  ports:
				  	- containerPort: 80
				  	  name: nginxport
```

