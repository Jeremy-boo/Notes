# Docker 代理设置指北

> 有时候因为网络原因，针对k8s.gcr.io 拉取不下来，需要配置代理，发现配置了宿主机的http_proxy，docker pull 并不能生效，
>
> 这里针对docker 来说，需要给docker daemon配置代理客户端。

## Docker代理

**执行docker pull时候，是由守护进程dockerd 来执行，因此代理需要配置在dockerd 的环境中，而这个环境受systemd 管控，因此实际是配置systemd**

```shell
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo touch /etc/systemd/system/docker.service.d/proxy.conf
```

**在proxy.conf 中添加如下内容**

**sudo vi /etc/systemd/system/docker.service.d/proxy.conf **

```shell
[Service]
Environment="HTTP_PROXY=http://192.168.156.66:7890"
Environment="NO_PROXY=localhost,127.0.0.1"
```

> 如果是容器运行阶段，需要代理上网，则需要配置 **~/.docker/config.json**， 一下配置只在docker 17.07及以上版本生效

```shell
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://192.168.156.66:7890",
     "noProxy": "localhost,127.0.0.1"
   }
 }
}
```



**重启Docker生效**

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

