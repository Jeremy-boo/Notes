# 日常开发指南

```
highAvailability:
  enable: true
database:
  type: external
  external:
    nodeSelector:
      key1: val1
    tolerations:
    - key: "key1"
      operator: "Exists"
      effect: "NoSchedule"

redis:
  type: external
  external:
    nodeSelector:
      key1: val1
    tolerations:
    - key: "key1"
      operator: "Exists"
      effect: "NoSchedule"
```





## devops-tool-operator本地构建问题

```
export BUNDLE_IMAGE=build-harbor.alauda.cn/test/devops/tools-operator-bundle
export TOOL_IMAGE=build-harbor.alauda.cn/test/devops/tools-operator
export INDEX_IMAGE=build-harbor.alauda.cn/test/devops/tools-operator-index
export builds=bin/amd64/manager
```



### Harbor Https 不能访问问题

```
# harbor 现存问题
ingress  证书 需要指定名称才能解析
```



## devops-apiserver 本地构建镜像

```
# devops-apiserver

export API_IMAGE=build-harbor.alauda.cn/test/devops/devops-api
export IMAGE=build-harbor.alauda.cn/test/devops/devops-apiserver

make push-image
make push-api-image


```



# 2. Devops-tool-operator csv 改动

1. [csv测试地址](http://cp-dev.myalauda.cn/operator-form/)

```
# 插件安装
docker create --entrypoint /bin/bash $(kubectl get deploy -n cpaas-system artifact-controller -o=jsonpath='{..image}') | xargs -I {} docker cp {}:/kubectl-artifact $(dirname $(which kubectl))

# 检查插件是否安装成功
kubectl plugin list

# 删除已有版本
kubectl delete artifactversion $(kubectl get artifactversion -A|grep tools | awk '{print $2}') -n cpaas-system

# 创建 artifactversion 

# 旧版本： 3.9.0-33+g823f834

kubectl artifact createVersion --artifact operatorhub-devops-tools-operator  --tag="v3.9-51-g7effdc87" --namespace cpaas-system

kubectl delete artifactversion $(kubectl get artifactversion -A|grep post | awk '{print $2}') -n cpaas-system

kubectl artifact createVersion --artifact operatorhub-postgres-operator  --tag="middleware-6280.2205261431" --namespace cpaas-system

# 重启catalog pod
kubectl delete pods $(kubectl get pods -A |grep cata | awk '{print $2}') -n cpaas-system
kubectl delete pods $(kubectl get pods -A |grep olm-operator | awk '{print $2}') -n cpaas-system

```



## 3. 代理

```
# 公司代理
export http_proxy=http://192.168.156.66:7890
export https_proxy=http://192.168.156.66:7890

export http_proxy=http://192.168.25.117:8118 
export https_proxy=http://192.168.25.117:8118

export http_proxy=
export https_proxy=

[Service]
Environment="HTTP_PROXY=http://192.168.156.66:7890"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```



## 4. 例会地址

```
#腾讯会议：680-7741-6891
```

