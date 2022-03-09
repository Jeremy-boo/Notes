# Notes
boo's personal notes



## Work Info

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


devops tool operator 上下架：
kubectl artifact createVersion --artifact devops-tools-operator --tag="v3.7-83-g19f3285" --namespace cpaas-system

kubectl artifact reSync --artifact devops-tools-operator
```



# Notes

```



docker buildx build --platform linux/arm64 --progress plain --output=type=docker --no-cache --pull=false --build-arg harbor_base_image_version=dev-arm --build-arg harbor_base_namespace=goharbor -f /root/debug/harbor-arm/src/github.com/goharbor/harbor/make/photon/notary-server/Dockerfile -t goharbor/notary-server-photon:dev-arm .

docker build --progress plain --no-cache --pull=false --build-arg harbor_base_image_version=dev-arm --build-arg harbor_base_namespace=goharbor -f /root/debug/harbor-arm/src/github.com/goharbor/harbor/make/photon/notary-server/Dockerfile -t goharbor/notary-server-photon:dev-arm .


# 启动本地docker
bash ./tests/showtime.sh ./tests/ci/api_common_install.sh 147.75.66.26 DB

#执行测试脚本
bash ./tests/showtime.sh ./tests/ci/api_run.sh DB 147.75.66.26

test脚本执行顺序：
docker run -i --privileged -v /root/actions-runner/harbor-arm-ci/harbor-arm/harbor-arm/src/github.com/goharbor/harbor-arm/src/github.com/goharbor/harbor/tests/ci/../../:/drone -v /root/actions-runner/harbor-arm-ci/harbor-arm/harbor-arm/src/github.com/goharbor/harbor-arm/src/github.com/goharbor/harbor/tests/ci/../:/ca -w /drone goharbor/harbor-e2e-engine:3.0.0-api robot --exclude proxy_cache -v DOCKER_USER: -v DOCKER_PWD: -v ip:147.75.195.254 -v ip1: -v http_get_ca:false -v HARBOR_PASSWORD:Harbor12345 /drone/tests/robot-cases/Group1-Nightly/Setup.robot /drone/tests/robot-cases/Group0-BAT/API_DB.robot
```

