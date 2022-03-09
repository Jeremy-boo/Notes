# 日常开发指南



## devops-apiserver 本地构建镜像

```
# devops-apiserver

export API_IMAGE=build-harbor.alauda.cn/test/devops/devops-api
export IMAGE=build-harbor.alauda.cn/test/devops/devops-apiserver

make push-image
make push-api-image


```



## 2. Devops-tool-operator csv 改动

1. [csv测试地址](http://cp-dev.myalauda.cn/operator-form/)

```
# 插件安装
docker create $(kubectl get deploy -n cpaas-system artifact-controller -o=jsonpath='{..image}') | xargs -I {} docker cp {}:/kubectl-artifact $(dirname $(which kubectl))

# 检查插件是否安装成功
kubectl plugin list

# 创建 artifactversion 
kubectl artifact createVersion --artifact operatorhub-devops-tools-operator  --tag="feat-harbor-high-availability.2203081119" --namespace cpaas-system
```



## 3. 代理

```
export http_proxy=http://192.168.156.66:7890
export https_proxy=http://192.168.156.66:7890

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

