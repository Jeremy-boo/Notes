## K8s集群搭建指北

### 环境准备

```

master_ip=192.168.140.74

k8s_version=1.21.4
echo '************* start ***************'


echo ' 1.基础环境初始化过程 '

echo '
 
'

cat <<EOF >> /tmp/env.sh
#!/bin/bash
 
echo '#关闭swap#####################################################################################'
swapoff -a
sed -ir '/swap/s/^/#/' /etc/fstab
echo '
 
'
echo '检查内存是否存在swap'
free -g
echo '
 
'
echo '#关闭并禁用防火墙#############################################################################'
systemctl stop firewalld.service
systemctl disable firewalld.service
systemctl status firewalld.service
echo '
 
'
echo '#关闭并禁用selinux############################################################################'
 
 
setenforce 0
sed -ri 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
getenforce
sestatus
echo '
 
'
echo '#配置系统时间同步############################################################################'
yum -y install chrony
systemctl enable chronyd
systemctl start chronyd.service
sed -i -e '/^server/s/^/#/' -e '1a server ntp.aliyun.com iburst' /etc/chrony.conf
systemctl restart chronyd.service
sleep 10
timedatectl
echo '
 
'
echo '#设置系统时区为东八区########################################################################'
timedatectl set-timezone Asia/Shanghai
date
 
echo '
 
'
 
echo '#修改系统内核参数############################################################################'
 
sed -i '/^vm.max_map_count/d' /etc/sysctl.conf
sed -i '/^net.ipv4.ip_forward/d'  /etc/sysctl.conf
echo 'vm.max_map_count = 262144' >> /etc/sysctl.conf
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
echo 'net.bridge.bridge-nf-call-ip6tables = 1' >> /etc/sysctl.conf
echo 'net.bridge.bridge-nf-call-iptables = 1' >> /etc/sysctl.conf
sysctl -p
 
echo '
 
'
 
yum -y install nfs-utils wget net-tools screen lsof tcpdump nc mtr openssl-devel vim bash-completion lrzsz nmap telnet tree bash-completion sysstat
 

echo "* soft nofile 655360" >> /etc/security/limits.conf
echo "* hard nofile 655360" >> /etc/security/limits.conf
echo "* soft nproc 655360"  >> /etc/security/limits.conf
echo "* hard nproc 655360"  >> /etc/security/limits.conf
echo "* soft  memlock  unlimited"  >> /etc/security/limits.conf
echo "* hard memlock  unlimited"  >> /etc/security/limits.conf
echo "DefaultLimitNOFILE=1024000"  >> /etc/systemd/system.conf 
echo "DefaultLimitNPROC=1024000"  >> /etc/systemd/system.conf

EOF

sh /tmp/env.sh

sleep 15


echo ' 2.安装和配置docker'

echo '
 
'

# 安装依赖包
yum install -y yum-utils device-mapper-persistent-data lvm2

#添加docker软件包的yum源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 直接安装Docker CE最新版

yum install docker-ce -y

systemctl restart docker &&  systemctl enable docker

#配置docker 使用阿里云加速
mkdir -p /etc/docker/
touch /etc/docker/daemon.json
> /etc/docker/daemon.json
cat <<EOF >> /etc/docker/daemon.json
{
"registry-mirrors":["https://q2hy3fzi.mirror.aliyuncs.com"]
}
EOF

systemctl daemon-reload && systemctl restart docker 

docker info 

sleep 15



echo ' 3.安装k8s基础组件'

echo '
 
'

#更换yum源为阿里源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF


#安装指定的k8s版本
yum install -y kubelet-$k8s_version kubeadm-$k8s_version kubectl-$k8s_version  --disableexcludes=kubernetes


# 启动k8s服务
systemctl enable kubelet && systemctl start kubelet
# 查看版本号
kubeadm version



master_ip=192.168.140.74

k8s_version=1.20.0

echo ' 4.初始化安装k8s'

echo '
 
'
echo '###初始化K8s需要花费几分钟'

kubeadm init --apiserver-advertise-address=$master_ip --image-repository=registry.aliyuncs.com/google_containers --kubernetes-version=$k8s_version --service-cidr=10.96.0.0/12 --pod-network-cidr=10.16.0.0/16 --ignore-preflight-errors=all | grep -E "kubeadm join|--d" > /tmp/join_master.sh



mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

sleep 15

kubectl get nodes

kubectl get pod --all-namespaces


sleep 15


echo ' 5.部署k8s集群网络组件kube-ovn'


echo '
 
'
echo '###安装kube-ovn组件需要花费几分钟'

wget https://gitee.com/mirrors/Kube-OVN/raw/release-1.8/dist/images/install.sh -P /tmp/

bash /tmp/install.sh


sleep 15



echo ' 6.k8s集群部署完成，集群状态检查'

kubectl get pod --all-namespaces

kubectl get nodes



echo '************* finished ***************'

```

