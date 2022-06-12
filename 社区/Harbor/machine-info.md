# Harbor 社区机器信息

## 

Linux 获取IP：

```
echo `ifconfig -a|grep inet|grep -v 127.0.0.1|grep -v inet6|awk '{print $2}'|tr -d "addr:" | head -n 1`
```



```
# harbor-arm-ci-03
ssh -i ~/.ssh/arm root@147.75.66.26

# harbor-arm-ci-04
ssh -i ~/.ssh/arm root@147.75.195.254
```

## Else

**Node 环境安装**

```
wget -qO- https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```



## back

clean.sh

```shell
#!/bin/bash
set -x

# harbor make path for harbor-arm
harbor_arm_path="$0/make"

# docker-compose file name
docker_compose_file="docker-compose.yml"

# docker-compose file path
docker_compose_path=$harbor_arm_path/$docker_compose_file

# Determine whether there is a deployed harbor process
if [ $(docker ps -a | grep -c "goharbor") -gt 1 ]; then
  if [ -f "$docker_compose_path" ]; then
    echo "Stop harbor process from docker-compose.yaml"
    cd $harbor_arm_path
    docker-compose down
  else
    echo "No docker-compose.yaml file"
  fi
else
    echo "No harbor process"
fi

# Determine whether the automated test process remains
if [ $(docker ps -a | grep -c "goharbor/harbor-e2e-engine") -gt 0 ]; then
  docker rm $(docker ps -qa)
else
    echo "No harbor process"
fi

# Delete cache file "/data"
harbor_cache_path="/data"
if [ -d "$harbor_cache_path" ]; then
    echo "delete harbor cache path $harbor_cache_path"
    rm -rf $harbor_cache_path/*
fi

# Delete the harbor-arm source directory
# harbor_arm_source_path="/root/actions-runner/harbor-arm-ci/harbor-arm/harbor-arm"
# if [ -d "$harbor_arm_source_path" ]; then
#     echo "delete harbor source code path $harbor_arm_source_path"
#     rm -rf $harbor_arm_source_path
# fi
```
