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

10.关于文档
主要的文档有：MDS(架构、调度、copyset replication、分配等)、client(架构、io路径、nebd等)、chunkserver(架构、multi-raft、cow等)、snapshotcloneserver(架构、任务管理和执行)

### 大家有问题欢迎给我们提issue