# Harbor社区双周会纪要



## Harbor

```
psql -h10.32.140.84 -p9999 -Upostgres 
mH0nX4vT9lX5vX4sF9lP7hY6aB5vB4uV




good afternoon everyone,

Everyone's here, let's get started.

I haven't had much progress on my side of the harbor performance working group since the last meeting。except read harbor source code

Do you have anything to sync?

By the way, at the harbor multi-architecture working group yesterday, Wang Yan said that the vm team is discussing matters related to audit log optimization， Chenyu, do you know about this?

Do I still need to mention the proposal for optimizing the audit log?



```



## Harbor社区会议讨论主题：

Harbor CI 剩余Target 调试问题

PR review 问题

Harbor 2.4 启动时间

Offline tar 问题

贡献记录如何查找





## Harbor社区提议：

1. Harbor用户使用手册与白皮书
2. harbor 有没有办法给所有登录用户一个所有项目的只读权限
2. Harbor机器人账户是否可以有访问项目列表的权限



## 2022年1月份双周会主持

### Harbor多架构工作小组

#### 上周工作进展：

1. 完成harbor-arm 构建指引文档

#### 本次会议讨论主题：

1. Photon 4.0中pg版本升级到14后，notary-sever和notary-signer 兼容问题预计什么时候可以修复
2. Harbor 2.5.0 版本是否有计划做harbor的备份与恢复机制
   1. 在k8s 上做数据备份与恢复(tangzu)
3. 社区是否有关于habor备份与恢复相关的讨论

近期目标：

第一个arm版本 release，pq问题已经要解决了，



### Harbor性能工作小组

#### 上周工作进展

1. 完成k8s集群中 harbor性能测试指标收集

#### 本次会议讨论主题

1. Harbor性能测试工具能够支持设置 prepare 和 push/pull 场景向 harbor push的镜像的 articact 大小
2. harbor 数据规模中的配置项（如：项目数量、每个项目镜像仓库数量、用户数量）是否支持可配
3. harbor 2.5.0 性能问题解决进展同步（https://github.com/goharbor/harbor/issues/16014）
