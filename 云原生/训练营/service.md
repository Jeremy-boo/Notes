# Service对象

## Linux 内核处理数据包： Netfilter 框架

- 应用层
- 网络协议层
- 网络层
- 数据链路层


## iptables 

五链(PREROUTING、INPUT、OUTPUT、POSTROUTING)四表

自上而下，一个一个去执行。

-A： add 添加， -m： 备注   -j： 跳转动作

跟负载均衡相关的： 使用ipvs

跟防火墙的相关的：使用iptables


**kube-proxy 工作原理**



