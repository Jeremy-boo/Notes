# CURL访问K8s集群

## K8s 管理工具

日常工作中 我们都是通过kubectl 或者 k8s-client 访问k8s集群，其实通过curl 也可以实现相同功能

> Tips： k8s 配置文件地址 /root/.kube/config

## 方式一： 通过证书访问k8s 集群

> 根据kubeconfig 生成访问集群凭据

```
cat config | grep certificate-authority-data | awk '{print $2}' | base64 -d > ca.crt
cat config | grep client-certificate-data | awk '{print $2}' | base64 -d > client.crt
cat config | grep client-key-data | awk '{print $2}' | base64 -d > client.key
APISERVER=$(cat config | grep server | awk '{print $2}')
```

> 访问k8s apiserver

```
curl -k --cert ./client.crt --cacert ./ca.crt --key ./client.key $APISERVER
```

**也可以通过k8s该方式访问其他k8s apiserver 接口**







