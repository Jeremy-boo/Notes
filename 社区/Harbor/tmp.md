## 社区会议讨论纪要

## Harbor高可用性能问题排查

性能测试脚本执行：

```
/home/start.sh --envfile /home/medium-perf-test.env  --volum /home/user.properties:/jmeter/apache-jmeter-5.2.1/bin/user.properties

192.168.140.34
```



高可用Harbor 环境信息：

```
http://192.168.138.84/console-platform/manage-platform/toolchain-management/integration/detail/harbor;integrationClassName=harbor
password@123
admin@cpaas.io

https://jira.alauda.cn/browse/SERVER-1106

Harbor地址：
http://wy-harbor.alauda.cn/
admin
Harbor123
```



单实例Harbor:

```
http://weiya-staging.alauda.cn
admin
Harbor12345

Harbor数据准备
```



### Harbor 2.5.0 性能测试

```
export HARBOR_URL=http://admin:Harbor12345@192.168.140.74:30004

export HARBOR_SIZE=medium

# 代理设置
export http_proxy=http://192.168.25.117:8118
export https_proxy=http://192.168.25.117:8118
export no_proxy='192.168.140.74'

nohup ./mage prepare > perf.log 2>&1 &

10.110.235.43:14268
```



#### 讨论议题

- Harbor-arm code review
- Harbor ARM仓库独立维护自己的构建Makefile，不再用sed 命令修改harbor的makefile
- Harbor-ARM 仓库维护问题

#### action

- 清理harbor-arm 仓库中 tests 目录下多余的脚本

- 

  



### update_db

```
_update_db:
	@echo "update goharbor db Dockerfile.base"
	@$(SEDCMDI) 's/RUN tdnf install -y shadow gzip postgresql/RUN tdnf install -y shadow gzip postgresql13/g' $(HARBOR_PHOTON_DB_PATH)
	@$(SEDCMDI) 's/usr\/share\/postgresql\/postgresql.conf.sample/usr\/pgsql\/13\/share\/postgresql.conf.sample/g' $(HARBOR_PHOTON_DB_PATH)
	@$(SEDCMDI) '14 a \\t&& ln -s /usr/pgsql/13/bin/* /usr/bin/ \\' $(HARBOR_PHOTON_DB_PATH)
```
