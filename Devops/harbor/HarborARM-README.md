# harbor-arm

Build Harbor for arm architecture.

## Build

<hr>
**System Requirements:**

**On a Linux host**: docker 19+  and support docker buildx



第一步： clone 代码

```
git clone https://github.com/goharbor/harbor-arm.git
```



第二步：下载harbor源代码：

```
cd harbor-arm && make download
```



第三步： 编译redis

```
make compile_redis
```



第四步： 准备arm相关数据

```
make prepare_arm_data
```



第五步： 准备替换ARM构建参数

```
make pre_update
```



第六步：编译harbor组件

```
make compile COMPILETAG=compile_golangimage
```



第七步： 镜像构建

```
make build GOBUILDTAGS="include_oss include_gcs" BUILDBIN=true NOTARYFLAG=true TRIVYFLAG=true CHARTFLAG=true GEN_TLS=true PULL_BASE_FROM_DOCKERHUB=false
```



**## Install & Run**

**System requirements:**

**On a Linux host:** docker 17.06.0-ce+ and docker-compose 1.18.0+ .