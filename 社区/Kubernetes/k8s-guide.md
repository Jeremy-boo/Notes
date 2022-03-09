## Kubernetes 社区贡献指北

[官方指引文档](https://github.com/kubernetes/community/tree/master/contributors/devel#readme)

> Tips： 前提条件： 本地安装了golang 开发环境

## 1. 开始步骤

### 1.1  克隆Kubernetes 代码到自己github 仓库



### 1.2  从本地github 仓库克隆 K8s代码

```
mkdir -p $GOPATH/k8s.io
cd $GOPATH/k8s.io

git clone https://github.com/$user/kubernetes.git
# or: git clone git@github.com:$user/kubernetes.git

cd kubernetes
git remote add upstream https://github.com/kubernetes/kubernetes.git
# or: git remote add upstream git@github.com:kubernetes/kubernetes.git

# Never push to upstream master
git remote set-url --push upstream no_push

# Confirm that your remotes make sense:
git remote -v
```



### 1. 3  Fetch and Rebase

```
git fetch upstream
git checkout master
git rebase upstream/master
```



### 1.4  基于master 新建分支做bug fix 或者feature 功能开发

```
git checkout -b myfeature
```



###  1.5  在该分支上做相关开发或者修复工作



### 1.6  提交本地修改内容到github 仓库(克隆之后的仓库)



### 1.7  github上创建pr



