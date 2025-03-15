## Git 问题汇总

### 1. 本地clone 出现EOF等情况如何解决

```
git config --global --add core.compression -1
```



### 2. Clone 大项目时候clone 不下来问题

```
git clone git@github.com:Jeremy-boo/kubernetes.git --depth 1

git fetch --unshallow

hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git cherry-pick --continue".
hint: You can instead skip this commit with "git cherry-pick --skip".
hint: To abort and get back to the state before "git cherry-pick",
hint: run "git cherry-pick --abort".
```





