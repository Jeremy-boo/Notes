# 日常开发指南

## 代理

```
export https_proxy=https://192.168.144.12:7890
export http_proxy=http://192.168.144.12:7890

export https_proxy=""
export http_proxy=""

alaudabot
UE8pRwodk3XPfkpo2K-z
```



## 测试环境

```
Gitea
http://192.168.130.62:31101
gitea_admin Gitea12345!
Harbor
http://192.168.130.62:31220
admin Harbor12345
nexus
http://192.168.130.62:31360
admin admin
sonarqube
http://192.168.130.62:31145
admin Sonarqube12345
gitlab
http://192.168.130.62:31210
root   Gitlab12345

jFrog:

http://192.168.17.28:18081

admin/Jfrog12345

子账号：ares/Jfrog12345

gitlab-ce: bozhu  9KEyGTabFJtGuocHQoiw
```


## devops-tool-operator本地构建问题



```
export BUNDLE_IMAGE=192.168.138.171:32110/devops/tools-operator-bundle
export TOOL_IMAGE=192.168.138.171:32110/devops/tools-operator
export INDEX_IMAGE=192.168.138.171:32110/devops/tools-operator-index

export builds=bin/amd64/manager
```

### Harbor Https 不能访问问题

## devops-apiserver 本地构建镜像

```
# devops-apiserver

export API_IMAGE=build-harbor.alauda.cn/test/devops/devops-api
export IMAGE=build-harbor.alauda.cn/test/devops/devops-apiserver

make push-image
make push-api-image

docker create --entrypoint /bin/bash quay.io/skopeo/stable:v1.3.0 | xargs -I {} docker cp {}:/skopeo $(dirname $(which skopeo))

skopeo copy --dest-tls-verify=false --src-tls-verify=false --all --dest-username admin --dest-password Harbor12345 docker://registry.alauda.cn:60080/devops/alpine:sha256:a3ce4bd24d88e3bf290fce38067adf165f92965832cbfe350ea95ba47676fe65 docker://192.168.137.5:30000/ops/alpine:3.0.2

```

# 2. Devops-tool-operator csv 改动

1. [csv测试地址](http://cp-dev.myalauda.cn/operator-form/)

```
# 插件安装
docker create --entrypoint /bin/bash $(kubectl get deploy -n cpaas-system artifact-controller -o=jsonpath='{..image}') | xargs -I {} docker cp {}:/kubectl-artifact $(dirname $(which kubectl))

23f359d2d578468836b24a90d433f85e1786cd836c32ee45373957094c536c3c
# 检查插件是否安装成功
kubectl plugin list

build-harbor.alauda.cn/devops/tools-operator-bundle:fix-jenkin.2212051429
# 删除已有版本


kubectl artifact createVersion --artifact operatorhub-katanomi-operator  --tag="v3.14.0-beta.78.gf57c105b" --namespace cpaas-system


kubectl delete artifactversion $(kubectl get artifactversion -A|grep kata | awk '{print $2}') -n cpaas-system


kubectl artifact createVersion --artifact operatorhub-katanomi-operator  --tag="v3.14.0-fix.1254.1.geb0e8c18-feat-add-chart-build" --namespace cpaas-system



# 重启catalog pod
kubectl delete pods --force --grace-period=0 $(kubectl get pods -A |grep cata | awk '{print $2}') -n cpaas-system
kubectl delete pods --force --grace-period=0 -n cpaas-system -l app=olm-operator


192.168.136.121:11443  

v3.10.0-0-g14c70883

registry.alauda.cn:60080/devops/tools-operator:hotfix-sam.2208020310
registry.alauda.cn:60080/devops/tools-operator-bundle:hotfix-sam.2208020310


registry.alauda.cn:60080/devops/tools-operator:feat-resource-allocation.2207191437
registry.alauda.cn:60080/devops/tools-operator-bundle:feat-resource-allocation.2207191437 
```

替换harbor-operator

```
kubectl delete artifactversion $(kubectl get artifactversion -A|grep harbor | awk '{print $2}') -n cpaas-system

kubectl artifact createVersion --artifact operatorhub-harbor-operator  --tag="v3.12.15-hotfix.746.1.ga24ad0e6-hotfix-webhook-verify" --namespace cpaas-system

kubectl delete pods --force --grace-period=0 $(kubectl get pods -A |grep cata | awk '{print $2}') -n cpaas-system
kubectl delete pods --force --grace-period=0 -n cpaas-system -l app=olm-operator
```


```
curl --request POST --header "PRIVATE-TOKEN: 8Qw3RdiXyHnSgbZA5f5y" "https://gitlab-ce.alauda.cn/api/v4/projects/86/statuses/59c2fc66787caaa32b2dbf9f821059eb17d189f9?state=success

curl --header "PRIVATE-TOKEN: 8Qw3RdiXyHnSgbZA5f5y" "https://gitlab-ce.alauda.cn/api/v4/projects/86/pipelines"

curl --header "PRIVATE-TOKEN: 8Qw3RdiXyHnSgbZA5f5y" "https://gitlab-ce.alauda.cn/api/v4/projects/86/pipelines/383865"


curl --request PUT --header "PRIVATE-TOKEN: 8Qw3RdiXyHnSgbZA5f5y" --header "Content-Type: application/json" --data '{"status": "success"}' "https://gitlab-ce.alauda.cn/api/v4/projects/86/pipelines/383865"



```