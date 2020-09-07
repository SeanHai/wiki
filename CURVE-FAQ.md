1. curve的roadmap是什么？
> 2020年下半年的计划是进行性能优化、稳定性优化、功能优化(discard、工具完善等)；后续有 rocksdb+raft替代etcd, 与云原生数据库相结合，对象存储，ec, 作为ceph的cache等计划

2. curve当前开源版本是否和内部使用版本一致？
> 当前开源版本与内部版本是一致的。我们希望后续的开发也是在github上直接进行

3. curve的元数据存储为什么使用etcd? etcd的性能会不会成为瓶颈？
> 当初出于人力和时间成本考虑，所有的方案是在当时情况下的最优选择，但也许不是最佳方案。etcd性能不会成为瓶颈，元数据的交互不在关键io路径上，并且元数据也都缓存在内存中

4. curve需要使用c++14版本？
> curve有用到c++14的特性，集中在chunkserver和nbd两处。为了大家更方面的使用，我们计划在后面版本中去掉c++14的依赖。

5. curve的稳定性如何？
> curve块存储在网易上线400+天，业务方面既有云主机、也有云盘；在线上业务正常运行阶段做过异常演练(进程挂掉、拔网线、拔盘、断电源等软硬件故障)，未出现丢数据，并且抖动在秒级；curve有很完善的测试体系，包括单元测试、集成测试、异常测试、大压力多级故障注入，每天、每周都会运行，这个也帮助我们发现了很多的corner case, 目前我们正在整理专题文档，这个也包括在内。

6. 部署遇到ssh权限问题，配置下ssh免密登录。部署遇到ansible语法错误，切换ansible版本到2.5.9
>  https://github.com/opencurve/curve/issues/4

7. 怎么对接k8s?
> 目前是类似ceph rbd-nbd的方式，我们有一个nbd，支持物理机map curve盘后使用，k8s需要对接map这种方式

8. 对海量小文件的支持怎么样？
> 目前提供的是块存储

9. curve是否有对接缓存机制？（bcache或者client缓存）
> 底层用的是ssd，暂时没有缓存机制

10. 安装curve openssl版本要求如何？
> 版本1.1.0以上

11. 再次部署是否需要clean？
> 需要，方法参考：https://github.com/opencurve/curve/blob/master/curve-ansible/README.md#41-%E6%B8%85%E7%90%86%E9%9B%86%E7%BE%A4

12. 安装nbd和nebd报错：Failed to connect to the host via ssh: ssh: connect to host 192.168.160.110 port 1046 connection refused
> 修改curve/curve-ansible/client.ini 中 ansible_ssh_port为ssh端口，默认22

13. 由于创建文件大小小于10G而报错（create fail. ret = -24）
> 创建文件大小最小10G

14. 如果硬盘是5T的，只格式化1%（server.ini [chunkservers:vars] chunk_alloc_percent=1）, 那永远只有1%可用吗？还是可用的有5T，只是后面的写会慢一点？
> 只有1%可用，但是可以扩容

15. curve直接使用braft的文件日志，带来的数据双写问题对性能影响大吗？
> 有一定影响，正在优化中

16. 运行清理后，fstab里面的挂载没清除，如果盘用作别的可能导致系统重启后进入紧急模式
> 正在改进

17. 内核版本是必须要3.15以上吗？
> 有配置项可以控制，可以在server.ini的[chunkservers:vars]里增加一个chunkserver_fs_enable_renameat2=false。或者手动把chunlserver机器上的/etc/curve/chunkserver.conf里面的变量改一下再重启（但是要用到nbd的话，内核版本要求比较高）

18. 客户端内核>=4.18.0-193.el8.x86_64，是为了使客户端内核支持nbd吗？
> 是的

19. curve对于大IO的性能较差？
> 大io的情况下 client的参数需要调整global.fileIOSplitMaxSizeKB=64 大io情况下切分粒度调大/sys/block/nbd0/queue/max_sectors_kb 配合上面的修改

20. 关于文档
主要的文档有：MDS(架构、调度、copyset replication、分配等)、client(架构、io路径、nebd等)、chunkserver(架构、multi-raft、cow等)、snapshotcloneserver(架构、任务管理和执行)

### 大家有问题欢迎给我们提issue