# Linux面试题整理

## 进程与线程相关

### Q: 进程、线程、协程区别？
**A:**
- **进程**：操作系统资源分配的基本单位，拥有独立的内存空间和系统资源，进程间通信需要特殊的IPC机制，进程切换开销大。
- **线程**：CPU调度的基本单位，同一进程内的线程共享进程的内存空间和资源，线程间通信相对简单，可直接访问共享内存，线程切换开销小于进程切换。
- **协程**：用户态的轻量级线程，由应用程序自己调度，切换开销极小，无需内核参与，同一线程内的协程共享线程资源，不能利用多核CPU，但在I/O密集型应用中效率高。

### Q: 僵尸进程是什么？
**A:** 僵尸进程是指子进程已经终止，但其父进程尚未调用wait()或waitpid()系统调用来获取子进程的终止状态信息，导致子进程的进程描述符仍然保留在系统中。僵尸进程不再占用系统资源（除了进程表中的一个条目），但如果大量僵尸进程累积，可能会耗尽进程ID资源。

### Q: 什么是进程中断？
**A:** 进程中断是指CPU暂停当前正在执行的进程，转而执行其他代码（如中断处理程序）的机制。中断可以由硬件（如I/O设备）或软件（如系统调用）触发。

### Q: 什么是软中断、硬中断？
**A:**
- **硬中断**：由硬件设备触发的中断信号，优先级高，会立即打断CPU当前执行的任务，处理时间要求短，通常只进行最必要的操作。
- **软中断**：由操作系统内核或程序触发的中断，优先级低于硬中断，可以被硬中断打断，通常用于处理硬中断后的后续工作，如网络数据包处理。

### Q: 什么是不可中断进程？
**A:** 不可中断进程（D状态，也称为TASK_UNINTERRUPTIBLE）是指进程正在等待I/O操作完成，且不响应任何信号（包括SIGKILL）的状态。这种状态通常是临时的，但如果系统出现问题（如I/O设备故障），进程可能会长时间处于此状态。

### Q: top命令里面可以看到进程哪些状态？
**A:** 在top命令输出中，进程状态主要有以下几种：
- R (Running)：运行中或可运行
- S (Sleeping)：可中断睡眠
- D (Disk sleep)：不可中断睡眠，通常是I/O等待
- T (Stopped)：被暂停的进程
- Z (Zombie)：僵尸进程
- I (Idle)：空闲的内核线程

### Q: 什么是进程最大数、最大线程数、进程打开的文件数，怎么调整？
**A:**
- **进程最大数**：系统允许同时存在的最大进程数，由kernel.pid_max参数控制。
- **最大线程数**：系统级由kernel.threads-max参数控制，用户级由ulimit -u或/etc/security/limits.conf中的nproc参数控制。
- **进程打开的文件数**：系统级由fs.file-max参数控制，进程级由ulimit -n或/etc/security/limits.conf中的nofile参数控制。

调整方法：
1. 临时调整：使用sysctl命令或ulimit命令
   ```
   sysctl -w kernel.pid_max=xxxxx
   ulimit -n 65535
   ```

2. 永久调整：修改/etc/sysctl.conf或/etc/security/limits.conf文件
   ```
   # /etc/sysctl.conf
   kernel.pid_max = xxxxx
   fs.file-max = xxxxx
   
   # /etc/security/limits.conf
   * soft nofile 65535
   * hard nofile 65535
   ```

### Q: Linux中的进程间通信的方式及其使用场景？
**A:**
1. **管道(Pipe)**：单向通信，用于有亲缘关系的进程，场景：shell命令的管道连接。
2. **命名管道(FIFO)**：可用于无亲缘关系的进程，场景：客户端-服务器通信。
3. **信号(Signal)**：用于通知进程发生了某事件，场景：终止进程、暂停进程。
4. **消息队列**：存储在内核中的消息链表，场景：异步通信，不需要进程同时运行。
5. **共享内存**：最快的IPC方式，场景：需要大量数据交换的应用。
6. **信号量**：用于进程同步，场景：控制对共享资源的访问。
7. **套接字(Socket)**：可用于不同主机间的进程通信，场景：网络通信、分布式应用。

### Q: Linux中的进程优先级与设置方法？
**A:** Linux系统中的进程优先级主要有两种表示方式：
1. **nice值**：范围从-20到19，值越小优先级越高，默认为0
2. **实时优先级**：范围从0到99，值越大优先级越高

设置方法：
1. 使用nice命令启动新进程并设置优先级：`nice -n 10 command`
2. 使用renice命令调整已运行进程的优先级：`renice +10 -p PID`
3. 使用chrt命令设置实时优先级：
   ```
   chrt -f 50 command  # 设置FIFO实时调度策略
   chrt -r 50 command  # 设置RR实时调度策略
   ```

## 内存相关

### Q: 什么是栈内存和堆内存？
**A:**
- **栈内存**：由编译器自动分配和释放，存储函数参数、局部变量等，空间有限但访问速度快，内存连续，大小固定。
- **堆内存**：由程序员手动分配和释放，存储动态创建的对象，空间较大但访问速度相对较慢，内存不连续，大小可变。

### Q: 什么是内存分页和分段？
**A:**
- **内存分页**：将物理内存和虚拟内存划分为固定大小的块（页），页是内存管理的基本单位（通常为4KB），通过页表实现虚拟地址到物理地址的映射，优点是减少内存碎片，支持虚拟内存。
- **内存分段**：将内存划分为不同长度的段（如代码段、数据段、堆栈段等），段是程序的逻辑单位，大小可变，通过段表实现虚拟地址到物理地址的映射，优点是符合程序的逻辑结构，便于共享和保护。

### Q: jvm内存如何查看？
**A:** 可以使用以下工具查看JVM内存：
1. **jstat**：显示JVM内存使用统计
   ```
   jstat -gc PID 1000
   ```
2. **jmap**：生成堆转储快照
   ```
   jmap -heap PID
   jmap -dump:format=b,file=heap.bin PID
   ```
3. **jconsole/jvisualvm**：图形化工具，提供内存使用的实时监控
4. **Java Flight Recorder (JFR)**：低开销的性能监控工具
   ```
   jcmd PID JFR.start
   ```

### Q: buffers与cached的区别？
**A:**
- **buffers**：主要用于缓存磁盘数据块的元数据（如inode、文件系统等），通常较小。
- **cached**：主要用于缓存磁盘数据块的实际内容，通常较大，用于加速文件读取。

两者都是为了提高I/O性能，可以被系统回收用于其他用途。

### Q: du和df统计不一致原因？
**A:**
- **df命令**：显示文件系统的磁盘使用情况，基于文件系统的超级块信息
- **du命令**：显示目录或文件的磁盘使用情况，通过遍历文件系统计算

不一致的原因：
1. 文件被删除但仍被进程打开：df会显示空间已释放，但du不会计算这部分空间
2. 文件系统中的保留块：df会计算，但du不会
3. 硬链接：du可能会多次计算同一个文件
4. 隐藏文件或目录：du可能会漏计算
5. 文件系统损坏：可能导致统计信息不准确

## 网络相关

### Q: http错误码和原因？
**A:** 常见HTTP错误码：
- **1xx**：信息性状态码
  - 100 Continue：继续请求
  - 101 Switching Protocols：协议切换

- **2xx**：成功状态码
  - 200 OK：请求成功
  - 201 Created：已创建
  - 204 No Content：无内容返回

- **3xx**：重定向状态码
  - 301 Moved Permanently：永久重定向
  - 302 Found：临时重定向
  - 304 Not Modified：资源未修改

- **4xx**：客户端错误状态码
  - 400 Bad Request：请求语法错误
  - 401 Unauthorized：未授权
  - 403 Forbidden：禁止访问
  - 404 Not Found：资源不存在
  - 405 Method Not Allowed：方法不允许
  - 429 Too Many Requests：请求过多

- **5xx**：服务器错误状态码
  - 500 Internal Server Error：服务器内部错误
  - 502 Bad Gateway：网关错误
  - 503 Service Unavailable：服务不可用
  - 504 Gateway Timeout：网关超时

### Q: 长连接、短连接、WebSocket区别和使用场景？
**A:**
- **短连接**：每次请求都建立新的TCP连接，请求完成后立即关闭，适用于简单的请求-响应模式，访问频率低的场景。
- **长连接**：建立TCP连接后保持一段时间，可以发送多个请求，HTTP/1.1默认使用长连接（Keep-Alive），适用于频繁请求同一服务器的场景。
- **WebSocket**：建立在TCP之上的全双工通信协议，建立连接后，服务器可以主动向客户端推送数据，适用于实时应用（聊天、游戏、股票行情等）。

### Q: nginx性能优化有哪些方式？
**A:**
1. **配置优化**：
   - 开启gzip压缩
   - 配置适当的worker_processes和worker_connections
   - 开启sendfile、tcp_nopush、tcp_nodelay
   - 配置keepalive_timeout和keepalive_requests

2. **缓存优化**：
   - 配置proxy_cache和fastcgi_cache
   - 使用expires和cache-control头
   - 配置open_file_cache

3. **连接优化**：
   - 使用epoll事件模型
   - 调整backlog队列
   - 优化超时设置

4. **静态文件处理**：
   - 使用try_files指令
   - 配置静态文件缓存
   - 使用CDN分发静态资源

5. **负载均衡优化**：
   - 使用upstream模块实现负载均衡
   - 配置健康检查
   - 使用least_conn或ip_hash等负载均衡算法

6. **系统优化**：
   - 调整系统内核参数（如somaxconn、tcp_tw_reuse等）
   - 使用SSD存储
   - 增加系统资源（CPU、内存）

### Q: lvs、nginx、haproxy区别和使用场景？
**A:**
- **LVS (Linux Virtual Server)**：工作在网络层（第4层），性能最高，可支持百万级并发，功能相对简单，不支持复杂的负载均衡算法，适用于需要超高性能的大型网站集群。
- **Nginx**：既可以工作在网络层也可以工作在应用层（第7层），性能高，可支持数万级并发，功能丰富，可作为Web服务器、反向代理、负载均衡器，适用于中小型网站、API网关、静态资源服务器。
- **HAProxy**：专注于负载均衡，支持第4层和第7层，性能高，可支持数万级并发，提供详细的统计信息和健康检查，适用于需要精细控制负载均衡策略的场景，如电子商务网站。

### Q: 什么是nginx的异步非阻塞？
**A:** Nginx采用异步非阻塞的事件驱动模型：
- **异步**：一个操作不需要等待另一个操作完成
- **非阻塞**：I/O操作不会导致进程/线程挂起等待

Nginx使用事件通知机制（如epoll）来处理连接，一个worker进程可以同时处理多个连接，而不需要为每个连接创建新的进程或线程。当一个连接上的I/O操作无法立即完成时，Nginx会注册一个事件处理器，然后继续处理其他连接，当I/O操作完成时，事件处理器会被触发执行相应的回调函数。

这种模型使Nginx能够用少量的系统资源处理大量的并发连接，是其高性能的关键。

### Q: linux网络丢包怎么排查？
**A:**
1. **检查网络接口状态**：
   ```
   ifconfig eth0
   ethtool eth0
   ```
   查看是否有错误计数器增加

2. **检查网络流量**：
   ```
   sar -n DEV 1
   iftop
   ```
   查看是否有网络拥塞

3. **检查系统负载**：
   ```
   top
   vmstat 1
   ```
   查看CPU、内存使用情况

4. **检查网络连接状态**：
   ```
   netstat -s
   ss -s
   ```
   查看TCP/IP协议栈统计信息

5. **检查防火墙规则**：
   ```
   iptables -L -n -v
   ```
   查看是否有丢弃数据包的规则

6. **使用tcpdump抓包分析**：
   ```
   tcpdump -i eth0 -n host x.x.x.x
   ```

7. **检查内核参数**：
   ```
   sysctl -a | grep net
   ```
   查看网络相关内核参数

8. **使用ping和traceroute测试连通性**：
   ```
   ping target_ip
   traceroute target_ip
   ```

9. **检查网卡队列**：
   ```
   ethtool -S eth0
   ```
   查看网卡队列是否溢出

### Q: MAC地址IP地址如何转换？
**A:** MAC地址和IP地址的转换通过ARP（Address Resolution Protocol）协议实现：

1. **IP到MAC的转换（ARP协议）**：
   - 当设备需要发送数据到同一网段的另一个IP地址时
   - 首先查询本地ARP缓存表
   - 如果没有对应记录，发送ARP广播请求
   - 目标IP的设备回应自己的MAC地址
   - 发送方更新ARP缓存表并发送数据

2. **查看ARP缓存表**：
   ```
   arp -a
   ```

3. **反向ARP（RARP）**：
   - 从MAC地址获取IP地址
   - 主要用于无盘工作站等场景

4. **DHCP也涉及MAC和IP的关联**：
   - DHCP服务器根据MAC地址分配IP地址
   - 可以配置固定MAC地址和IP地址的映射

## 系统管理与优化

### Q: Linux系统中/proc是做什么的？
**A:** /proc是一个虚拟文件系统，不占用磁盘空间，提供了对内核数据结构的访问接口。主要功能：

1. **提供系统信息**：如CPU、内存、磁盘等
   - /proc/cpuinfo：CPU信息
   - /proc/meminfo：内存信息
   - /proc/loadavg：系统负载

2. **提供进程信息**：每个进程都有对应的目录/proc/[pid]
   - /proc/[pid]/status：进程状态
   - /proc/[pid]/cmdline：命令行
   - /proc/[pid]/fd/：文件描述符

3. **提供内核参数访问**：通过/proc/sys目录
   - 可以通过读写这些文件来查看或修改内核参数

4. **提供网络信息**：
   - /proc/net/tcp：TCP连接信息
   - /proc/net/route：路由表

### Q: load和cpu使用率区别？
**A:**
- **CPU使用率**：表示CPU在一段时间内被占用的百分比，范围是0-100%（多核系统可能显示为0-100% * 核心数），反映CPU的繁忙程度。
- **系统负载（Load Average）**：表示系统中处于可运行状态和不可中断状态的进程平均数，通常显示1分钟、5分钟和15分钟的平均值，理想情况下，负载值应接近CPU核心数。

区别：
- CPU使用率只反映CPU资源使用情况
- 系统负载反映整体系统资源（CPU、I/O等）的竞争情况
- 高CPU使用率不一定导致高负载（如CPU密集型任务）
- 高负载不一定导致高CPU使用率（如I/O等待）

### Q: 常用的性能分析诊断命令？
**A:**
1. **top/htop**：实时显示系统资源使用情况和进程信息
2. **vmstat**：显示系统内存、进程、CPU等信息
   ```
   vmstat 1 10  # 每秒输出一次，共10次
   ```
3. **iostat**：显示CPU和磁盘I/O统计信息
   ```
   iostat -x 1 10
   ```
4. **mpstat**：显示多处理器CPU使用情况
   ```
   mpstat -P ALL 1 10
   ```
5. **sar**：收集、报告和保存系统活动信息
   ```
   sar -u 1 10  # CPU使用率
   sar -r 1 10  # 内存使用率
   sar -n DEV 1 10  # 网络统计
   ```
6. **dstat**：系统资源统计工具，可替代vmstat、iostat等
   ```
   dstat -cdngy 1 10
   ```
7. **netstat/ss**：显示网络连接、路由表等信息
   ```
   netstat -tuln
   ss -tuln
   ```
8. **iotop**：类似top，但专注于I/O使用情况
9. **strace**：跟踪进程的系统调用和信号
   ```
   strace -p PID
   ```
10. **perf**：Linux性能分析工具
    ```
    perf top
    perf record -a sleep 10
    perf report
    ```

### Q: 如何管理和优化内核参数？
**A:**
1. **查看当前内核参数**：
   ```
   sysctl -a
   cat /proc/sys/path/to/parameter
   ```

2. **临时修改内核参数**：
   ```
   sysctl -w parameter=value
   echo value > /proc/sys/path/to/parameter
   ```

3. **永久修改内核参数**：
   - 编辑/etc/sysctl.conf或/etc/sysctl.d/目录下的配置文件
   - 应用更改：`sysctl -p`

4. **常见优化参数**：
   - 网络相关：
     ```
     net.ipv4.tcp_fin_timeout = 30
     net.ipv4.tcp_keepalive_time = 1200
     net.ipv4.tcp_max_syn_backlog = 8192
     net.core.somaxconn = 65535
     ```
   
   - 内存相关：
     ```
     vm.swappiness = 10
     vm.dirty_ratio = 40
     vm.dirty_background_ratio = 10
     ```
   
   - 文件系统相关：
     ```
     fs.file-max = 655350
     fs.inotify.max_user_watches = 524288
     ```

### Q: 如何创建和管理自定义systemd服务？
**A:**
1. **创建服务单元文件**：
   在/etc/systemd/system/目录下创建.service文件，例如myservice.service：
   ```
   [Unit]
   Description=My Custom Service
   After=network.target

   [Service]
   Type=simple
   User=myuser
   ExecStart=/path/to/my/program
   Restart=on-failure
   RestartSec=5s

   [Install]
   WantedBy=multi-user.target
   ```

2. **重新加载systemd配置**：
   ```
   systemctl daemon-reload
   ```

3. **启动服务**：
   ```
   systemctl start myservice
   ```

4. **设置开机自启**：
   ```
   systemctl enable myservice
   ```

5. **查看服务状态**：
   ```
   systemctl status myservice
   ```

6. **停止服务**：
   ```
   systemctl stop myservice
   ```

7. **查看服务日志**：
   ```
   journalctl -u myservice
   ```

### Q: Linux内核模块的加载与卸载过程？
**A:**
**加载过程**：
1. 使用insmod或modprobe命令加载模块
   ```
   insmod module.ko
   modprobe module
   ```
2. 内核验证模块签名（如果启用了模块签名验证）
3. 内核分配内存并加载模块代码
4. 解析模块依赖关系（modprobe会自动处理依赖）
5. 调用模块的init函数进行初始化
6. 将模块添加到已加载模块列表中

**卸载过程**：
1. 使用rmmod或modprobe -r命令卸载模块
   ```
   rmmod module
   modprobe -r module
   ```
2. 检查模块是否正在使用
3. 调用模块的exit函数进行清理
4. 释放模块占用的内存
5. 从已加载模块列表中移除模块

**查看已加载模块**：
```
lsmod
```

**查看模块信息**：
```
modinfo module
```

## 存储相关

### Q: 常见的raid有哪些，使用场景是什么？
**A:**
- **RAID 0（条带化）**：数据分散存储在多个磁盘上，优点是提高读写性能，缺点是无冗余，一个磁盘故障会导致所有数据丢失，适用于需要高性能但数据安全性要求不高的场景，如临时数据处理。
- **RAID 1（镜像）**：数据完全复制到多个磁盘，优点是提供完全冗余，读性能提升，缺点是存储效率低，只有50%的空间可用，适用于需要高可靠性的关键数据存储。
- **RAID 5**：数据和奇偶校验信息分布在所有磁盘上，优点是平衡了性能和冗余，可承受一个磁盘故障，缺点是写性能较差，重建时间长，适用于需要平衡性能和可靠性的通用存储。
- **RAID 6**：类似RAID 5，但使用双重奇偶校验，优点是可承受两个磁盘同时故障，缺点是写性能更差，存储效率低于RAID 5，适用于大容量存储阵列，数据安全性要求高的场景。
- **RAID 10（1+0）**：RAID 1和RAID 0的组合，优点是兼具RAID 0的性能和RAID 1的冗余，缺点是存储效率低，只有50%的空间可用，适用于需要高性能和高可靠性的数据库服务器。

### Q: lvm怎么划分？
**A:** LVM (Logical Volume Manager) 划分步骤：

1. **创建物理卷(PV)**：
   ```
   pvcreate /dev/sdb /dev/sdc
   ```

2. **创建卷组(VG)**：
   ```
   vgcreate myvg /dev/sdb /dev/sdc
   ```

3. **创建逻辑卷(LV)**：
   ```
   lvcreate -L 10G -n mylv myvg
   ```

4. **格式化逻辑卷**：
   ```
   mkfs.ext4 /dev/myvg/mylv
   ```

5. **挂载使用**：
   ```
   mount /dev/myvg/mylv /mnt
   ```

LVM管理命令：
- 查看物理卷：`pvdisplay`
- 查看卷组：`vgdisplay`
- 查看逻辑卷：`lvdisplay`
- 扩展卷组：`vgextend myvg /dev/sdd`
- 扩展逻辑卷：`lvextend -L +5G /dev/myvg/mylv`
- 调整文件系统大小：`resize2fs /dev/myvg/mylv`

### Q: lsof命令使用场景？
**A:** lsof (list open files) 命令的使用场景：

1. **查看进程打开的文件**：
   ```
   lsof -p PID
   ```

2. **查看指定文件被哪些进程打开**：
   ```
   lsof /path/to/file
   ```

3. **查看指定用户打开的文件**：
   ```
   lsof -u username
   ```

4. **查看指定程序打开的文件**：
   ```
   lsof -c program_name
   ```

5. **查看网络连接**：
   ```
   lsof -i
   lsof -i:80  # 查看80端口
   ```

6. **查找已删除但仍被进程占用的文件**：
   ```
   lsof | grep deleted
   ```

7. **查看指定目录下被打开的文件**：
   ```
   lsof +D /path/to/directory
   ```

## 文本处理工具

### Q: grep sed awk cut组合使用？
**A:** 这
