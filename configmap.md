*Key value store* that lives as a k8s object that lives in etcd
`key: value`

# create
`kubectl create configmap configname --from-litteral=verbose=true`
`kubectl create configmap configname --from-file=directory` key is the file name, content is the content of the file. etcd max 1m

# consume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: appname
spec:
  containers:
    - image: imagename
      name: appname
      volumeMounts:
      - name: config-volume
        mountPaths: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: specfial-config
```

# caveats
- name of the config map consumed must be known at pod creation
- update latency up to 1 min
- not easily composable
- if you reference a configmap that isn't there and isn't optional, the container will stall with the usual uniformative k8s error message

One way to consume config map dynamically is to access the k8s api from inside the pod with a small k8s client to bring more flexibility and composability 

