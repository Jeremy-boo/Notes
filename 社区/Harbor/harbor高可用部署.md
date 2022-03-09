# Harbor高可用部署指北-Helm篇

## 1. 前置准备工作



## 2. Harbor Helm Chart下载

```
git clone https://github.com/goharbor/harbor-helm.git
```



### 3. Harbor 高可用配置文件

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
externalURL: http://192.168.137.237:30004
persistence:
  persistentVolumeClaim:
    registry:
      existingClaim: 
      storageClass: "dev-test"
      subPath: "registry"
    chartmuseum:
      existingClaim: 
      storageClass: "dev-test"
      subPath: "chartmuseum"
    jobservice:
      existingClaim: 
      storageClass: "dev-test"
      subPath: "jobservice"
    database:
      existingClaim: 
      storageClass: "dev-test"
      subPath: "database"
    redis:
      existingClaim: 
      storageClass: "dev-test"
      subPath: "redis"

nginx:
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-nginx-photon
    tag: alauda-v2.2.3-127
  replicas: 2
  
portal:
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-harbor-portal
    tag: alauda-v2.2.3-127
  replicas: 2

core:
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-harbor-core
    tag: alauda-v2.2.3-127
  replicas: 2
  ## Startup probe values
  startupProbe:
    enabled: true
    initialDelaySeconds: 10
 
jobservice:
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-harbor-jobservice
    tag: alauda-v2.2.3-127
  replicas: 2
  maxJobWorkers: 10

registry:
  registry:
    image:
      repository: idcbuild-harbor.alauda.cn/devops/goharbor-registry-photon
      tag: alauda-v2.2.3-127
  controller:
    image:
      repository: idcbuild-harbor.alauda.cn/devops/goharbor-harbor-registryctl
      tag: alauda-v2.2.3-127
  replicas: 2

chartmuseum:
  enabled: true
  absoluteUrl: false
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-chartmuseum-photon
    tag: alauda-v2.2.3-127
  replicas: 2

trivy:
  enabled: true
  image:
    repository: idcbuild-harbor.alauda.cn/devops/goharbor-trivy-adapter-photon
    tag: alauda-v2.2.3-127
  replicas: 2

notary:
  enabled: true
  server:
    image:
      repository: idcbuild-harbor.alauda.cn/devops/goharbor-notary-server-photon
      tag: alauda-v2.2.3-127
    replicas: 2
  signer:
    image:
      repository: idcbuild-harbor.alauda.cn/devops/goharbor-notary-signer-photon
      tag: alauda-v2.2.3-127
    replicas: 2

database:
  type: external
  external:
    host: "192.168.131.221"
    port: "31494"
    username: "postgres"
    password: "iM1nA9cS1zO5oP2bA9mI7sQ0jS1tT5gP"
    coreDatabase: "registry"
    notaryServerDatabase: "notary_server"
    notarySignerDatabase: "notary_signer"
    sslmode: "disable"
  maxIdleConns: 100
  maxOpenConns: 900

redis:
  type: external
  external:
    addr: "192.168.137.237:30781,192.168.137.237:31256,192.168.137.237:31005"
    sentinelMasterSet: "mymaster"
    coreDatabaseIndex: "0"
    jobserviceDatabaseIndex: "1"
    registryDatabaseIndex: "2"
    chartmuseumDatabaseIndex: "3"
    trivyAdapterIndex: "5"
    password: "12345"

```

