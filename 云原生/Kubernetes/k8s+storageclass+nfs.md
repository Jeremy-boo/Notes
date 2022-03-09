# K8S 搭建 NFS+ StorageClass指北

**搭建StorageClass+NFS,大致有以下几个步骤:**

```
创建一个可用的NFS Server
创建Service Account.这是用来管控NFS provisioner在k8s集群中运行的权限
创建StorageClass.负责建立PVC并调用NFS provisioner进行预定的工作,并让PV与PVC建立管理
创建NFS provisioner.有两个功能,一个是在NFS共享目录下创建挂载点(volume),另一个则是建了PV并将PV与NFS的挂载点建立关联
```



## 1. 创建NFS共享服务

**该步骤比较简单，不在这里赘述**

**当前NFS Server 信息**

```
IP: 192.168.136.71 
Export Path: /data/share
```



## 2. 创建Service Account权限账户

**kubectl apply -f rbac.yaml**

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default        #根据实际环境设定namespace,下面类同
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
    # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
```

## 3. 创建NFS资源的StorageClass

**kubectl apply -f nfs-storageclass.yaml**

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: fuseim.pri/ifs #这里的名称要和provisioner配置文件中的环境变量PROVISIONER_NAME保持一致
parameters:  
	archiveOnDelete: "false"
```

## 4. 创建NFS provisioner

**kubectl apply -f nfs-provisioner.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default  #与RBAC文件中的namespace保持一致
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nfs-client-provisioner
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: fuseim.pri/ifs  #provisioner名称,请确保该名称与 nfs-StorageClass.yaml文件中的provisioner名称保持一致
            - name: NFS_SERVER
              value: 192.168.136.71   #NFS Server IP地址
            - name: NFS_PATH  
              value: /data/share   #NFS挂载卷
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.136.71  #NFS Server IP地址
            path: /data/share     #NFS 挂载卷
```



## 5. 创建测试pod，检查是否部署成功

**1. pod + pvc**

创建PVC

**test-pvc.yaml**

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: test-pvc
  annotations:
    volume.beta.kubernetes.io/storage-class: "managed-nfs-storage"   #与nfs-StorageClass.yaml metadata.name保持一致
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Mi
```

**确定PVC为Bound状态**

```
kubectl get pvc

kubectl get pv
```

**创建测试pod，检查pvc是否可以正常挂载**

**test-pod.yaml**

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: test-pod
spec:
  containers:
  - name: test-pod
    image: busybox:1.24
    command:
      - "/bin/sh"
    args:
      - "-c"
      - "touch /mnt/SUCCESS && exit 0 || exit 1"   #创建一个SUCCESS文件后退出
    volumeMounts:
      - name: nfs-pvc
        mountPath: "/mnt"
  restartPolicy: "Never"
  volumes:
    - name: nfs-pvc
      persistentVolumeClaim:
        claimName: test-pvc  #与PVC名称保持一致
```



## 6. 常见问题

**错误1：**

```
 provision "default/test-claim" class "managed-nfs-storage": unexpected error getting claim reference: selfLink was empty, can't make reference
```

kubernetes1.20在v1.20之后默认删除了 metadata.selfLink 字段，然而，

部分应用仍然依赖于这个字段，例如 nfs-client-provisioner； 

需要在/etc/kubernetes/manifests/kube-apiserver.yaml 添加

--feature-gates=RemoveSelfLink=false 重新启用metadata.selfLink字段；

```yaml
spec:
  containers:
  - command:
    - kube-apiserver
    - --feature-gates=RemoveSelfLink=false
```

**kubectl apply -f /etc/kubernetes/manifests/kube-apiserver.yaml**



