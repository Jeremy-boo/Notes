## GVM-Golang版本管理工具



## 1. GVM介绍

>  github 上开源项目[gvm](https://github.com/moovweb/gvm)，目前已经拥有7000多star，使用 gvm 不需要在关心下载完新版本的 Go 后,自己还需要手动配置环境变量。



## 2. 安装GVM

> 如果你正在使用bash 或者zsh，可以使用如下命令安装

```
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```



## 3. GVM 命令介绍

1. gvm list 查看已经安装的版本和当前使用的版本
2. gvm listall 命令，查看当前所有go的版本
3. gvm install go1.18rc1 安装指定golang版本
4. gvm use go1.18rc1 指定当前系统使用的版本



## 4. 让gvm 开机自动启动

> 写入~/.zshrc 或者~/.bashrc 文件中

```
# gvm settings 当前gvm地址
[[ -s "/Users/boo/.gvm/scripts/gvm" ]] && source "/Users/boo/.gvm/scripts/gvm"

export GOVERSION=go1.14

gvm install ${GOVERSION}
gvm use ${GOVERSION}
```



