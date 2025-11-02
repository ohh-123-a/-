```
kubectl create deployment --image=docker.io/library/nginx:latest --replicas=4 -o yaml --dry-run=client > test0010.yaml
```

```
kubectl crate deployment --image=docker.io/library/nginx:latest --replicas=10 -o json --dry-run=client > test0010.json
```

