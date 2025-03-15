# Integration Testing

## ginkgo


```
apiVersion: v1
data:
  config: |
    meta:
      baseURL: 'http://192.168.137.5:30000/'
    auth:
      type: kubernetes.io/basic-auth
      data:
        username: YWRtaW4=
        password: SGFyYm9yMTIzNDU=
    pluginService:
      namespace: katanomi-system
      name: gitlab-plugin
    repository:
      project: ops
      repo: test
kind: ConfigMap
metadata:
  name: e2e-config-harbor-plugin
  namespace: katanomi-e2e
```

