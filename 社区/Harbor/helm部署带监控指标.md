# Harbor 安装指北



## 1. Helm-Charts安装Harbor

### Tips： 前置准备工作

```
k8s 集群
nfs 存储
helm 安装
如果使用ingres方式安装harbor， 请安装trafik 或者 nginx-ingress
```



## 2. 下载Harbor-Helm

```
git clone https://github.com/goharbor/harbor-helm.git
```



## 3. 修改配置文件

```
cd harbor-helm
```

**test-values.yaml**

```yaml
expose:
  type: nodePort
  tls:
    enabled: false
  nodePort:
    ports:
      http:
        nodePort: 30004
      https:
        nodePort: 30005
      notary:
        nodePort: 30006
externalURL: http://192.168.140.74:30004
nginx:
  image:
    tag: v2.5.0-dev
  resources:
    requests:
      memory: 256Mi
      cpu: 500m
    limits:
      memory: 512Mi
      cpu: 1000m
portal:
  image:
    tag: v2.5.0-dev
  resources:
    requests:
      memory: 256Mi
      cpu: 500m
    limits:
      memory: 512Mi
      cpu: 1000m
core:
  image:
    tag: v2.5.0-dev
  resources:
    requests:
      memory: 256Mi
      cpu: 500m
    limits:
      memory: 2048Mi
      cpu: 1000m
jobservice:
  image:
    tag: v2.5.0-dev
  resources:
    requests:
      memory: 256Mi
      cpu: 500m
    limits:
      memory: 512Mi
      cpu: 1000m
registry:
  controller:
    image:
      tag: v2.5.0-dev
  registry:
    image:
      tag: v2.5.0-dev
    resources:
      requests:
        memory: 256Mi
        cpu: 500m
      limits:
        memory: 2048Mi
        cpu: 1000m
chartmuseum:
  image:
    tag: v2.5.0-dev
trivy:
  image:
    tag: v2.5.0-dev
notary:
  server:
    image:
      tag: v2.5.0-dev
    resources:
    requests:
      memory: 256Mi
      cpu: 500m
    limits:
      memory: 512Mi
      cpu: 1000m
  signer:
    image:
      tag: v2.5.0-dev
database:
  internal:
    image:
      tag: v2.5.0-dev
    resources:
      requests:
        memory: 256Mi
        cpu: 500m
      limits:
        memory: 4096Mi
        cpu: 2000m
redis:
  internal:
    image:
      tag: v2.5.0-dev
    resources:
      requests:
        memory: 256Mi
        cpu: 500m
      limits:
        memory: 512Mi
        cpu: 1000m
exporter:
  image:
    tag: v2.5.0-dev
persistence:
  persistentVolumeClaim:
    registry:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "registry"
    chartmuseum:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "chartmuseum"
    jobservice:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "jobservice"
    database:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "database"
    redis:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "redis"
    trivy:
      existingClaim:
      storageClass: "managed-nfs-storage"
      subPath: "trivy"
metrics:
  enabled: true
  core:
    path: /metrics
    port: 8001
  registry:
    path: /metrics
    port: 8001
  jobservice:
    path: /metrics
    port: 8001
  exporter:
    path: /metrics
    port: 8001
  serviceMonitor:
    enabled: true

trace:
  enabled: true
  provider: jaeger
  sample_rate: 1
  jaeger:
    # jaeger supports two modes:
    #   agent mode(uncomment endpoint and uncomment username, password if needed)
    #   collector mode(uncomment agent_host and agent_port)
    endpoint: http://10.110.235.43:14268/api/traces
```

执行如下命令：

**helm install -f values-prod.yaml test-harbor . --namespace=harbor**



