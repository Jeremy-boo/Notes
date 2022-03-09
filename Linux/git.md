## Git 问题汇总

### 1. 本地clone 出现EOF等情况如何解决

```
git config --global core.compression 0
```



### 2. Clone 大项目时候clone 不下来问题

```
git clone git@github.com:Jeremy-boo/kubernetes.git --depth 1

git fetch --unshallow
```

