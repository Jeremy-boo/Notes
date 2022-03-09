## CCE-SWR集成



### 1. CCE SWR 特点

- 一个组织下可以拥有多个镜像空间

- 不同组织之间的镜像空间权限不同

  

### 2. CCE 内部default-secret 

- 组织只会存在global集群
- 组织下的项目可以在各个集群中创建(项目-> 集群中的ns； ns 中会带有default-secret凭据信息)



### 3. CCE-SWR原有集成逻辑





### 4. 实现细节问题

绑定接口: /api/v1/imageregistrybinding/qqq  post



获取远程仓库： api/v1/imageregistrybinding/qqq/tool/remote-repositories-project  get







