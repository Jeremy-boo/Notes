# Kuberbetes 基础复习

## 1. 容器基础本质

> 容器，其实是一种特殊的进程而已。

- Cgroups技术
- Namespace技术
  - PID Namespace
  - Mount Namespace
  - Network Namespace
  - User Namespace
  - UTS Namespace
  - IPC Namespace

### 1.1 隔离与限制

#### 1.1.1 基础概念

> 虚拟机 和 容器比较

- 虚拟机使用虚拟技术作为应用沙盒，必须有Hypervisor创建虚拟机，这个虚拟机真实存在，带来额外的资源消耗和占用。

- 用户在虚拟机对应用的调用，不可避免的需要经过虚拟化软件的拦截和处理，本身又是一层资源消耗

- 容器化的用户应用，只是宿主机上的一个普通进程(虚拟化的消耗不存在)；但是使用Namespace技术隔离(隔离性没有虚拟机高)

> 容器化的不足

- Namespace 隔离性没有虚拟机高

- 宿主机上的容器共享Linux内核，容器的内核版本和 宿主机需要保持一致

- Linux 内核中，很多对象不能被Namespace 化，比如时间(容器内修改时间，系统时间也会被修改)

- 安全问题

#### 1.1.2 限制-Cgroups

> Linux Cgoups 全称是Linux Control Group, 主要作用是限制一个进程能够使用的资源上限，包括CPU、内存、磁盘、网络带宽等。

Cgoups 给用户暴露的操作接口是文件系统，以文件和目录的方式组织在操作系统的 /sys/fs/cgroup路径下。

```
# mount 命令展示
$ mount -t cgroup
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
```

**限制使用**

- cpu.cfs_period_us 和 cpu.cfs_quota_us 配合使用限制cpu的使用量
