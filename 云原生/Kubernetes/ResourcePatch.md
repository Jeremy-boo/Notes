# ResourcePatch

```
apiVersion: operator.alauda.io/v1alpha1
kind: ResourcePatch
metadata:
  creationTimestamp: "2021-12-10T03:20:18Z"
  finalizers:
  - resourcepatch
  generateName: rp-
  generation: 16
  labels:
    target: 401e26eebb0abb342bdcbe3fe172f59c
  name: rp-ssz6b
  resourceVersion: "43097412"
  uid: 2ccd711d-49ab-4278-a83a-7f14ab51d915
spec:
  jsonPatch:
  - op: replace
    path: /spec/template/spec/containers/0/image
    value: 192.168.131.82:60080/solutions/devops-apiserver:custom-yhsys-3.6.2112211557
  - op: add
    path: /spec/template/spec/serviceAccount
    value: devops-apiserver
  - op: replace
    path: /spec/template/spec/initContainers/0/image
    value: 192.168.131.82:60080/solutions/pipeline-templates:custom-yhsys-3.6.2112211557
  release: cpaas-system/alauda-devops
  target:
    apiVersion: apps/v1
    kind: Deployment
    name: devops-controller
    namespace: cpaas-system
status:
  lastApplyTime: "2021-12-21T08:53:32Z"
  message: ""
  phase: Active
```

