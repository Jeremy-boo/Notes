## Harbor的备份与恢复

### 1. 数据准备



#### Perf 准备Harbor数据

```
export HARBOR_URL=http://admin:Harbor12345@192.168.131.221:32456
export HARBOR_SIZE=small

#no_proxy 设置
export no_proxy="192.168.131.221"
```



###### 机器人账户密码

```
hqchv0HahEJdO9XfNYj7JOY8lwecohKj
```



### 2. 复制pvc数据