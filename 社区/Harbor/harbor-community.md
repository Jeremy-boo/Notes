

# Harbor社区贡献计划

## 一、目标

1. 提升参与度和影响力：希望大家聊起harbor就会想起我们公司，就像现在说起k8s自然会知道 google, redhat 比较大的贡献者
2. 推动社区解决我们的问题：建立闭环，同步我们客户遇到的问题和需求，跟社区一起解决，包括提供一些建议。
3. harbor企业版：我们会以企业版的形式售卖harbor并提供相关的服务和兜底。



[Harbor贡献度统计](https://harbor.devstats.cncf.io/d/66/developer-activity-counts-by-companies?orgId=1&search=open&folder=current)



## 二、现状

### 2.1 目前灵雀云参与并主导两个工作组的内容。

1. [Multi Architecture](https://github.com/goharbor/community/tree/master/workgroups/wg-multiarch)
2. [Performance](https://github.com/goharbor/community/tree/master/workgroups/wg-performance)

### 2.2 Harbor Multi Architecture Work Group

#### Harbor multi architecture 目前取得的进展：

1. harbor arm 构建 makefile 编写，pr 已合并
2. e2e 测试脚本支持harbor arm 测试, pr 已提, 待合并
3. harbor ci workflow调试，12月初已跑通所有case，pr 已提 带合并

#### Block Item:

1. harbor 依赖的notary-server 和notary-signer 组件近期升级了pq版本, 导致ci 构建测试时候 notary-server 和notary-signer 组件启动不成功，最后自动化用例关于这方面的失败，导致 ci workflow pr 不能合并

#### 近期目标：

1. 2021年末尾完成harbor 2.3.0 arm 版本第一个release
2. 2022 一月完成harbor 2.4.0版本的release 发布

#### 长期目标：

1. 推动multi architecture work group的发展，跟随harbor主版本发布的节奏

### 2.3 Harbor Performance Work Group

#### Harbor Performance Work Group 目前取得的进展

1. 在harbor 2.3.0的版本完成性能测试 perf 项目
2. 提供harbor 2.3.0 docker-compose 部署以及k8s 部署的harbor 性能测试报告
3. 提供harbor 2.4.0 docker-compose 部署以及k8s 部署的harbor 性能测试报告
4. 修复harbor 社区关于harbor 性能相关的issue
5. Docker-compose 部署harbor 增加宿主机采集cpu、memory 等性能指标信息

#### Harbor performance 近期目标

1. 增加harbor 部署在k8s中的性能指标信息采集功能
2. harbor 2.5.0 性能issue解决：https://github.com/goharbor/harbor/issues/16014

#### Harbor performance 长期目标

1. 解决harbor 社区性能issue相关问题
2. 维护harbor 长期稳定运行



## 三、Harbor社区近期计划

[Harbor RoadMap](https://github.com/orgs/goharbor/projects/1)

#### Harbor 2.5.0 重要目标：

1. [Integrate Sigstore Cosign into Harbor](https://github.com/goharbor/harbor/issues/15315)
2. [Interrogation Services - display supported/unsupported OS in Scan results](https://github.com/goharbor/harbor/issues/14384)
3. [Vulnerabilities Export Feature](https://github.com/goharbor/harbor/issues/14987)
4. [Get metric from trivy](https://github.com/goharbor/harbor/issues/14352)
5. [Better Webhook Management in Web UI](https://github.com/goharbor/harbor/issues/15363)
6. [Integrate Nydus with Harbor](https://github.com/goharbor/harbor/issues/14136)
7. [Harbor Replication is failing for large images as scanning is still in progress](https://github.com/goharbor/harbor/issues/15159)
8. [Introduce cache layer to improve performance](https://github.com/goharbor/harbor/issues/15937)
9. [Improve the database migration process](https://github.com/goharbor/harbor/issues/15642)
10. [Define live backup/restore workflow for Harbor](https://github.com/goharbor/harbor/issues/15975)
11. [enhance webhook granularity to repository level ](https://github.com/goharbor/harbor/issues/16001)

## 四、行动(如何达到我们内部目标)

考虑的点：

1. 如何提高影响力，参考redhat 如何接入k8s的
2. 做的事情与市场及时沟通，如何配套宣传，做到一个大的feature，或者第一个arm版本release等。
3. 与harbor社区人员建立 信任，这是第一步

### 参考redhat参与k8s开源项目

redhat 是google 准备将k8s 开源出来的时候，便和google 一直参与k8s的开发、维护和宣传工作；

与我们不同的点在于：我们是在harbor 已经很成熟的情况下，才参与到harbor的共同建设当中去，想要提高公司在harbor 社区的影响力难度可见。从目前的现状我建议从一下几方面行动：

### 具体行动建议

1. 从目前参与的work group 作为切入点，把控好目前work group的进度与产出(具体行动计划如下)
   1. 在harbor work group 投入更多的时间，定好每个迭代的目标，确定目标按时、按质完成
   2. 结合公司在harbor实践 给社区更多的反馈和建议
   3. 主动在slack 同步 各个work group的进展，加紧跟社区其他人的沟通
2. 实时关注harbor 社区进展与讨论，积极参与社区的讨论以及提出相关建议
   1. 积极关注社区的Roadmap 以及每个版本需要做的事情，如果跟公司目标一致的 可以积极参与一个feature的开发
3.  配合市场部的宣传，让更多的人知道我们在harbor社区做的事情



