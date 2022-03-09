## Harbor 安装指南

Acp 2.9 安装harbor 1.8版本

```
helm upgrade --install harbor --namespace kube-ops ./ \
    --set global.registry.address=${REGISTRY} \
    --set externalURL=http://${NODE_IP}:31104 \
    --set harborAdminPassword=$harbor_password \
    --set ingress.enabled=false \
    --set service.type=NodePort \
    --set service.ports.http.nodePort=31104 \
    --set service.ports.ssh.nodePort=31105 \
    --set service.ports.https.nodePort=31106 \
    --set database.password=$db_password \
    --set redis.usePassword=true \
    --set redis.password=$redis_password \
    --set database.persistence.enabled=false \
    --set database.persistence.host.nodeName=${NODE_NAME} \
    --set database.persistence.host.path=${HOST_PATH}/database \
    --set redis.persistence.enabled=false \
    --set redis.persistence.host.nodeName=${NODE_NAME} \
    --set redis.persistence.host.path=${HOST_PATH}/redis \
    --set chartmuseum.persistence.enabled=false \
    --set chartmuseum.persistence.host.nodeName=${NODE_NAME} \
    --set chartmuseum.persistence.host.path=${HOST_PATH}/chartmuseum \
    --set registry.persistence.enabled=false \
    --set registry.persistence.host.nodeName=${NODE_NAME} \
    --set registry.persistence.host.path=${HOST_PATH}/registry \
    --set jobservice.persistence.enabled=false \
    --set jobservice.persistence.host.nodeName=${NODE_NAME} \
    --set jobservice.persistence.host.path=${HOST_PATH}/jobservice \
    --set AlaudaACP.Enabled=false 
    
  更新harbor组件密码：
  helm upgrade harbor --namespace kube-ops ./ \
  	--set redis.usePassword=true \
    --set redis.password="test2222" \
    --set database.password="test12345" \
  
```

