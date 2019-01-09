Prometheus 横向扩展调研
--- 
  **说明：此项调研是关于Prometheus的横向扩容方面内容所进行的。**
  
  **当监控规模大到Promthues单台无法有效处理的情况下，可以选择利用Promthues的联邦集群的特性，将Promthues的监控任务划分到不同的实例当中。可以解决此问题！**
  
一. Prometheus 横向扩展可行性方案
--- 
  1. **基本HA ：服务可用性**
  ![HA](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LVSVjCoJ2ZKnv7dYe6V%2F-LPufOd4LgRt7mUDTV8C%2Fpromethues-ha-01.png?generation=1546683354194329&alt=media)
 
  基本的HA模式只能确保Promthues服务的可用性问题，但是不解决Prometheus Server之间的数据一致性问题以及持久化问题(数据丢失后无法恢复)，也无法进行动态的扩展。因此这种部署方式适合监控规模不大，Promthues Server也不会频繁发生迁移的情况，并且只需要保存短周期监控数据的场景。

  2. **基本HA + 远程存储**
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LVSVjCoJ2ZKnv7dYe6V%2F-LPufOd602YogyiIIadr%2Fprometheus-ha-remote-storage.png?generation=1546683353605259&alt=media)
  
  在解决了Promthues服务可用性的基础上，同时确保了数据的持久化，当Promthues Server发生宕机或者数据丢失的情况下，可以快速的恢复。 同时Promthues Server可能很好的进行迁移。因此，该方案适用于用户监控规模不大，但是希望能够将监控数据持久化，同时能够确保Promthues Server的可迁移性的场景。
  
  3.**基本HA + 远程存储 + 联邦集群**
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LVSVjCoJ2ZKnv7dYe6V%2F-LPufOd8-X3EaOQhjM7L%2Fprometheus-ha-rs-fedreation.png?generation=1546683353680741&alt=media)
  
  这种部署方式一般适用于两种场景：

  场景一：单数据中心 + 大量的采集任务

  这种场景下Promthues的性能瓶颈主要在于大量的采集任务，因此用户需要利用Prometheus联邦集群的特性，将不同类型的采集任务划分到不同的Promthues子服务中，从而实现功能分区。例如一个Promthues Server负责采集基础设施相关的监控指标，另外一个Prometheus Server负责采集应用监控指标。再有上层Prometheus Server实现对数据的汇聚。

  场景二：多数据中心

  这种模式也适合与多数据中心的情况，当Promthues Server无法直接与数据中心中的Exporter进行通讯时，在每一个数据中部署一个单独的Promthues Server负责当前数据中心的采集任务是一个不错的方式。这样可以避免用户进行大量的网络配置，只需要确保主Promthues Server实例能够与当前数据中心的Prometheus Server通讯即可。 中心Promthues Server负责实现对多数据中心数据的聚合。
  
  4. **三种方案选择对比**
  ![](http://m.qpic.cn/psb?/V131TXgR0JpSlk/LzMoQpm5Kc.SjqJTZkQtMCx*PUYSHrAxEBsd*3mhnAM!/b/dLsAAAAAAAAA&bo=swPeAAAAAAADF1w!&rf=viewer_4)
  
  - **可以按照自己的需求进行方案的选择，其中方式二应该更适合于我们当前集群。**
  
二. 专有名词解释
---
  1. **本地存储**
  
  ①. Prometheus 2.x 采用自定义的存储格式将样本数据保存在本地磁盘当中。如下所示，按照两个小时为一个时间窗口，将两小时内产生的数据存储在一个块(Block)中，每一个块中包含该时间窗口内的所有样本数据(chunks)，元数据文件(meta.json)以及索引文件(index)。：
  
  ![](http://m.qpic.cn/psb?/V131TXgR0JpSlk/fnvyJh8UBaDNg.okvf1EvmfYoSs5lK6CZ7a9LMhUVH4!/b/dLYAAAAAAAAA&bo=mwMaAQAAAAADF7E!&rf=viewer_4)
  
  ②. 本地存储配置可以通过命令行启动参数的方式进行配置，命令如下：
  
  ![](http://m.qpic.cn/psb?/V131TXgR0JpSlk/sd.C.yGXifmuz4Jqrvyap.qk1otAQNjN4TEbCOES4Xk!/b/dL4AAAAAAAAA&bo=qAPrAQAAAAADF3M!&rf=viewer_4)
  
  当前时间窗口内正在收集的样本数据，Prometheus则会直接将数据保存在内存当中。为了确保此期间如果Prometheus发生崩溃或者重启时能够恢复数据，Prometheus启动时会从写入日志(WAL)进行重播，从而恢复数据。此期间如果通过API删除时间序列，删除记录也会保存在单独的逻辑文件当中(tombstone)。
  
  在文件系统中这些块保存在单独的目录当中，Prometheus保存块数据的目录结构如下所示：
  
  ![](http://m.qpic.cn/psb?/V131TXgR0JpSlk/gfCPkMpD*PzDIYkT0FHfyqRd1zPAtgHWrQ1mt45m9DM!/b/dL8AAAAAAAAA&bo=qQLJAQAAAAADF1E!&rf=viewer_4)
    
  - 在一般情况下，Prometheus中存储的每一个样本大概占用1-2字节大小。如果需要对Prometheus Server的本地磁盘空间做容量规划时，可以通过以下公式计算:
    
    > needed_disk_space = retention_time_seconds * ingested_samples_per_second * bytes_per_sample
    
  - 从上面公式中可以看出在保留时间(retention_time_seconds)和样本大小(bytes_per_sample)不变的情况下，如果想减少本地磁盘的容量需求，只能通过减少每秒获取样本数(ingested_samples_per_second)的方式。因此有两种手段，一是减少时间序列的数量，二是增加采集样本的时间间隔。考虑到Prometheus会对时间序列进行压缩效率，减少时间序列的数量效果更明显。
    
  ③. 从失败中恢复：如果本地存储由于某些原因出现了错误，最直接的方式就是停止Prometheus并且删除data目录中的所有记录。当然也可以尝试删除那些发生错误的块目录，不过相应的用户会丢失该块中保存的大概两个小时的监控记录。
  
  2. **远程存储**
  
  ①. 本地存储的缺点：
  
  - Prometheus的本地存储设计可以减少其自身运维和管理的复杂度，同时能够满足大部分用户监控规模的需求。但是本地存储也意味着Prometheus无法持久化数据，无法存储大量历史数据，同时也无法灵活扩展和迁移
  
  ②. 远程存储：
    
  - 为了保持Prometheus的简单性，Prometheus并没有尝试在自身中解决以上问题，而是通过定义两个标准接口(remote_write/remote_read)，让用户可以基于这两个接口对接将数据保存到任意第三方的存储服务中，这种方式在Promthues中称为Remote Storage（远程存储）。
  
  3. **联邦集群**
  
  ①. 对于大部分监控规模而言，我们只需要在每一个数据中心(例如：EC2可用区，Kubernetes集群)安装一个Prometheus Server实例，就可以在各个数据中心处理上千规模的集群。同时将Prometheus Server部署到不同的数据中心可以避免网络配置的复杂性。
  
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LVS55BPP8pCGOwigu4D%2F-LVS5AfNN1MfzdymIFH0%2Fprometheus_feradtion.png?generation=1546676387737273&alt=media)
  
  ②. 在每个数据中心部署单独的Prometheus Server，用于采集当前数据中心监控数据。并由一个中心的Prometheus Server负责聚合多个数据中心的监控数据。这一特性在Promthues中称为联邦集群。
  
  ③. 联邦集群的特性可以帮助用户根据不同的监控规模对Promthues部署架构进行调整。例如如下所示，可以在各个数据中心中部署多个Prometheus Server实例。每一个Prometheus Server实例只负责采集当前数据中心中的一部分任务(Job)，例如可以将不同的监控任务分离到不同的Prometheus实例当中，再有中心Prometheus实例进行聚合。
  
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LVS55BPP8pCGOwigu4D%2F-LPufOxe8OObithx3ftP%2Fprometheus_feradtion_2.png?generation=1546676377255440&alt=media)
  
  功能分区，即通过联邦集群的特性在任务级别对Prometheus采集任务进行划分，以支持规模的扩展。
  
  更多细节方面请点击[这里](https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/readmd/prometheus-remote-storage)
---
  
