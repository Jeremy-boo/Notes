
# 基础概念

**什么是Devops**

用最简单的话来说，devops是产品开发过程中开发-Dev & Ops-运维团队之间的灰色区域，是一种跨团队之间的沟通、协作的文化，目的是让产品能够快速迭代。

**持续构建-CI，持续交付-CD 以及持续部署 & 持续测试 的概念**

对于ci，cd 以及部署、测试相关整体来讲都是为了能够让我们产品能够有快速迭代、上线的能力，如何来评论公司内部CI/CD 实践的如何，就看从代码开发，到测试部署到生产环境这个时间成本是多少。

**如何有效的实施 Devops**

定义Devops 工作流程。
1. 版本控制：存储和管理源代码的阶段
2. 持续集成：开发人员构建代码, 对齐编译，通过代码审查到单元测试以及集成测试通过
3. 持续交付： 持续集成的下一步，其中发布和测试过程完全是自动化的，cd 保证版本能够快速迭代
4. 持续部署：应用程序通过所有测试要求后，自动部署到生产环境，进行自动发布，无需人为干预

