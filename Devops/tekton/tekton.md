# Tekton

## 1. Tekton 初体验

### 1.1 tekton 安装

### 1.2 Tekton 实践

> tekton 主要资源对象介绍
- Task：表示执行命令的一系列有序的步骤，task 里可以定义一系列的 steps，例如编译代码、构建镜像、推送镜像等，每个 step 实际由一个 Pod 执行
- Pipeline：一组有序的 Task，Pipeline 中的 Task 可以使用之前执行过的 Task 的输出作为它的输入。表示一个或多个 Task、PipelineResource 以及各种定义参数的集合。
- TaskRun：Task 只是定义了一个模版，TaskRun 才真正代表了一次实际的运行，当然你也可以自己手动创建一个 TaskRun，TaskRun 创建出来之后，就会自动触发 Task 描述的构建任务。
- PipelineRun：类似 Task 和 TaskRun 的关系，`PipelineRun` 也表示某一次实际运行的 pipeline，下发一个 PipelineRun CRD 实例到 Kubernetes 后，同样也会触发一次 pipeline 的构建。
- ClusterTask：覆盖整个集群的任务，而不是单一的某一个命名空间，这是和 Task 最大的区别，其他基本上一致的。
- PipelineResource：表示 pipeline 输入资源，比如 github 上的源码，或者 pipeline 输出资源，例如一个容器镜像或者构建生成的 jar 包等。



> Example:


**task-test.yaml**

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: test
spec:
  resources:
    inputs:
      - name: repo
        type: git
  steps:
    - name: run-test
      image: golang:1.14-alpine
      workingDir: /workspace/repo
      command: ['go']
      args: ['test']
```



**Image-Sync-ClusterTask**

```yaml
apiVersion: tekton.dev/v1beta1
kind: "ClusterTask"
metadata:
  name: alauda-sync-artifacts
spec:
  description: >-
    sync images included in it.
  params:
    - name: src-image-addr
      description: URL of the image to be copied to the destination registry
      default: "build-harbor.alauda.cn/ops/nignx:guojing"
    - name: src-repo-auth
      description: "the secret name which contains the auth info"
      default: "build-harbor"
    - name: dst-image-addr
      description: URL of the image to be copied to the destination registry
      default: "demo.goharbor.io/ops/nignx:guojing"
    - name: dst-repo-auth
      description: "the secret name which contains the auth info"
      default: "actest-harbor"
    - name: namespace
      description: "namespace where the auth secrets is"
      default: "ait-dev"
    - name: verbose
      default: "false"
  results:
    - description: images list
      name: images
  steps:
   - image: build-harbor.alauda.cn/devops/kubectl-devops:master
     name: sync-artifacts
     resources:
       requests:
         cpu: 500m
         memory: 500Mi
       limits:
         cpu: 1000m
         memory: 1000Mi
     script: |
       #!/bin/sh
       if [ "$(params.verbose)" = "true" ]; then
         set -ex
       else
         set -e
       fi

       # get auth info
       if [ `kubectl auth can-i get secrets` != 'yes' ]; then
         echo "the default service account has no access to get secret $(params.src-repo-auth) and $(params.dst-repo-auth)"
         exit -1
       else
         SRCREPOUSERNAME=`kubectl -n $(params.namespace) get secret  $(params.src-repo-auth)  -oyaml | yq e '.data.username' - | base64 -d`
         SRCREPOPASSWD=`kubectl -n $(params.namespace) get secret  $(params.src-repo-auth)  -oyaml | yq e '.data.password' - | base64 -d`
         DSTREPOUSERNAME=`kubectl -n $(params.namespace) get secret  $(params.dst-repo-auth)  -oyaml | yq e '.data.username' - | base64 -d`
         DSTREPOPASSWD=`kubectl -n $(params.namespace) get secret  $(params.dst-repo-auth)  -oyaml | yq e '.data.password' - | base64 -d`
       fi

       # skopeo copy --all --src-tls-verify=false  --dest-tls-verify=false   --dest-username admin --dest-password Harbor12345 docker://$(params.src-repo)/${line#*/}  docker://demo.goharbor.io/${line#*/}
       skopeo copy --all --src-username $SRCREPOUSERNAME  $SRCREPOPASSWD --dest-username $DSTREPOUSERNAME --dest-password $DSTREPOPASSWD docker://$(params.src-image-addr) docker://$(params.dst-image-addr)


```



```
[ "skopeo copy --all --src-username $(SRC_IMG_USERNAME) ","--src-password $(SRC_IMG_PASSWORD) ","--dest-username $(DST_IMG_USERNAME) ","--dest-password $(DST_IMG_PASSWORD)"," docker://$(SRC_IMG_ADDR)"," docker://$(DST_IMG_ADDR)" ]
```

