# Jaeger Operator

> Jaeger Operator版本跟踪Jaeger组件(Query, Collector, Agent)的一种版本。 当发布Jaeger组件的新版本时,将发布一个新版本的operator,该operator了解如何将先前版本的运行实例升级到新版本。

## Kubernetes上安装Jaeger Operator

> 确保您的`kubectl`命令已正确配置为与有效的Kubernetes集群通信。 如果没有集群,则可以使用[`minikube`](https://kubernetes.io/docs/tasks/tools/install-minikube/)在本地创建一个群集。

**安装operator，请运行：**

```shell
mkdir jaeger-operator && cd jaeger-operator

wget https://github.com/jaegertracing/jaeger-operator/releases/download/v1.29.1/jaeger-operator.yaml

kubectl create namespace observability

kubectl apply -f jaeger-operator.yaml -n observability
```



**创建jaeger 实例：**

```yaml
apiVersion: jaegertracing.io/v1
kind: Jaeger
metadata:
  name: my-jaeger
  namespace: observability
spec:
  query:
    serviceType: NodePort
  collector:
    serviceType: NodePort
  strategy: allInOne
  allInOne:
    image: jaegertracing/all-in-one:latest
    options:
      log-level: debug
  storage:
    type: memory
    options:
      memory:
        max-traces: 100000
  ingress:
    enabled: true
    annotations:
      kubernetes.io/ingress.class: nginx
```

kubectl apply -f jaeger-instance.yaml -n observability