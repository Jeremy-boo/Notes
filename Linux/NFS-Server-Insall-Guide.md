## NFS-Server安装指北

### 1. 背景

Kubernetes 对 Pod 进行调度时，以当时集群中各节点的可用资源作为主要依据，自动选择某一个可用的节点，并将 Pod 分配到该节点上。在这种情况下，Pod 中容器数据的持久化如果存储在所在节点的磁盘上，就会产生不可预知的问题，例如，当 Pod 出现故障，Kubernetes 重新调度之后，Pod 所在的新节点上，并不存在上一次 Pod 运行时所在节点上的数据。

为了使 Pod 在任何节点上都能够使用同一份持久化存储数据，我们需要使用网络存储的解决方案为 Pod 提供 [数据卷](https://kuboard.cn/learning/k8s-intermediate/persistent/volume.html)。常用的网络存储方案有：NFS/cephfs/glusterfs。

### 2. 配置要求



Linux 服务器两台。示例： Centos70

- 一台作为nfs server
- 一台作为nfs 客户端

### 3. 配置NFS服务器

> 本章节所有命令都以root执行

Step one: 安装nfs软件包

```
yum install -y rpcbind nfs-utils
```

Step Two:  创建存储目录, 修改NFS配置文件

```
mkdir -p /data/share
chmod 666 /data/share
然后，修改 NFS 配置文件 /etc/exports
vim /etc/exports
/data/share *(rw,sync,insecure,no_subtree_check,no_root_squash)
```

Step Three:

```
systemctl enable rpcbind
systemctl enable nfs-server

systemctl start rpcbind
systemctl start nfs-server
```



Step Four: 检查配置是否生效 

```
exportfs
# 输出结果如下所示
/data/share    	<world>

## 修改/etc/exports，执行exportfs -rv 生效 
```



### 4. 客户端测试NFS

> TIP
>
> - 本章节中所有命令都以 root 身份执行

Step one:  安装nfs 客户端所需安装包

```
yum install -y nfs-utils

```

Step Two:  执行以下命令检查 nfs 服务器端是否有设置共享目录

```
# showmount -e $(nfs服务器的IP)
showmount -e 172.17.216.82
# 输出结果如下所示
Export list for 172.17.216.82:
/data/share *
 
```



Step three: 执行以下命令挂载 nfs 服务器上的共享目录到本机路径 `/root/nfsmount`

```
mkdir /root/nfsmount
# mount -t nfs $(nfs服务器的IP):/root/nfs_root /root/nfsmount
mount -t nfs 172.17.216.82:/root/nfs_root /root/nfsmount
# 写入一个测试文件
echo "hello nfs server" > /root/nfsmount/test.txt
```



Step Four: 在 nfs 服务器上执行以下命令，验证文件写入成功

```
cat /root/nfs_root/test.txt

```



