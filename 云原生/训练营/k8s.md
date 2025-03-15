# Kubernetes 学习

## 限流算法

- 漏斗算法
- 计数器固定窗口算法
- 令牌桶算法


> APIServer中的限流

- Max-requests-inflight: 在给定时间内的最大 non-mutating 请求数.
- Max-mutating-requests-inflight: 在给定时间内最大的 mutating 请求数，调整 apiserver 的流控 qos.


**传统限流方法的局限性**

- 粒度粗
  - 无法为不同用户，不同场景设置不同的限流
- 单队列
  - 共享
- 不公平
- 无优先级


**API Priority and Fairness**

- APF以更细粒度的方式对请求进行分类和隔离。
- 它还引入了空间有限的排队机制，因此在非常短暂的突发情况下，API服务器不会拒绝任何请求。
-  通过使用公平排队技术从队列中分发请求，这样，一个行为不佳的控制器就不会饿死其他控制 器(即使优先级相同)。



> 两个概念： FlowSchema  and  PriorityLevelConfiguration





## 2. 高可用APIServer

- apiserver 是一个无状态的 rest server
- 无状态就方便Scale Up /down
- 负载均衡
  - 多个apiserver 实例之上，配置负载均衡

**APIServer 注意的点**

- 预留足够的 CPU and Memory
- Retelimit
- 设置合适的缓存大小
- 客户端尽量使用长连接



## 3. Apimachinery

**GVK**

- Group
- Kind
- Version
  - Internel version 和 external version
  - 版本转换(k8s 只向前兼容3个版本)



