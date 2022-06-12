## Harbor漏洞修复

### TODO List

- Jobservice 漏洞修复(jobservice是因为beego版本太低，需要升级到v2.0.2 重新编译)
- Notary-signer-photon 是因为kubernetes 版本太低，需要升到版本 1.27.3
- Harbor-core 和 harbor-portal 漏洞为 shadow-utils 

```
# Harbor-DB 漏洞修复
cyrus-sasl 升级版本到 2.1.28
glib2	 升级版本到 2.65.3
gnutls	 升级版本到 3.7.1
libgcrypt 1.9.4
util-linux	2.37.4
shadow-utils 官方未修复


```

## Harbor二进制漏洞修复策略

背景：

Harbor的漏洞修复 内部在各个版本已经做了很多次了，比如3.4 针对华为修复harbor漏洞，3.8 修复的harbor 漏洞 以及这次针对ISDP 也在做一些漏洞修复；但是3.4 修复harbor 2.0 版本的漏洞 并没有办法复用到harbor 2.2.3 版本上。

针对ISDP当前做法：

- 针对harbor本身源码上依赖的二进制文件漏洞，找到二进制文件修复版本，更新harbor 依赖，重新编译 构建镜像。
- 针对Harbor 组件的一些漏洞，升级组件的二进制版本，然后重新构建镜像
- 其他，官方还没有修复的二进制文件(1. 走解释说明 2. 确定harbor组件没有用到，构建镜像后 删除该二进制文件)

我的想法是 针对ISDP Harbor的修复，只对华为做特殊交付，不合并到标准产品，标准产品的源码还是与社区的版本保持一致。

针对Harbor 这种漏洞修复，处于长期的考虑，静涛提出了一些建议：

1. 对Harbor 做源码层级的改动，针对ISDP 这次修改来说，是否可以跟宗航和 维维商量，这次就不修了
2. 我们从Harbor fork 一份代码出来，针对华为、烟草 这些 维护特定的代码分支，然后我们内部一直维护下去(如果修复漏洞的改动可以合并到社区，我们提pr 推动harbor 社区合并)

Daniel 和 玉坤 你们有什么看法呀？



