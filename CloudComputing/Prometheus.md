2019.1.3
---

一. Prometheus
===
  1. 在 Prometheus 中，每一个暴露监控样本数据的 HTTP 服务被称为一个实例。
  
  2. 可以通过 up 表达式查询当前所有 Instance 的状态，也可以通过 Prometheus UI 中的 Targets 页面查询当前所有监控采集的任务和各个任务下面的所有实例状态。
  
  3. Prometheus 的数据模型
  
    Prometheus会将所有采集到的样本数据以时间序列（time-series）的方式保存在内存数据库中，并且定时保存到硬盘上。time-series是按照时间戳和值的序列顺序存放的，我们称之为向量(vector). 每条time-series通过指标名称(metrics name)和一组标签集(labelset)命名。
    
    ①. 样本
      - 在 time series 中的每一个点称为一个样本（sample），样本由三部分组成：
        > 指标（metric）：metric name 和描述当前样本特征的 labelsets ；
        
        > 时间戳（timestamp）：一个精确到毫秒的时间戳。
        
        > 样本值（value）：一个 float64 的浮点型数据表示当前样本的值。
        
        > <--------------- metric ---------------------><-timestamp -><-value->
        
        > http_request_total{status="200", method="GET"}@1434417560938 => 94355
        
        > http_request_total{status="200", method="GET"}@1434417561287 => 94334
        
      - 指标（Metric）：
        > 指标格式：<metric name>{<label name>=<label value>, ...}
    
    ②. Metrics类型
      - Prometheus定义了4中不同的指标类型(metric type)：Counter（计数器）、Gauge（仪表盘）、Histogram（直方图）、Summary（摘要）。
      
      - Counter：只增不减的计数器
        > Counter类型的指标其工作方式和计数器一样，只增不减（除非系统发生重置）。常见的监控指标，如http_requests_total，node_cpu都是Counter类型的监控指标。 一般在定义Counter类型指标的名称时推荐使用_total作为后缀。
        
      - Gauge：可增可减的仪表盘
        > 与Counter不同，Gauge类型的指标侧重于反应系统的当前状态。因此这类指标的样本数据可增可减。常见指标如：node_memory_MemFree（主机当前空闲的内容大小）、node_memory_MemAvailable（可用内存大小）都是Gauge类型的监控指标。
      
        
      - 使用Histogram和Summary分析数据分布情况
      
        > 在大多数情况下人们都倾向于使用某些量化指标的平均值，例如CPU的平均使用率、页面的平均响应时间。这种方式的问题很明显，以系统API调用的平均响应时间为例：如果大多数API请求都维持在100ms的响应时间范围内，而个别请求的响应时间需要5s，那么就会导致某些WEB页面的响应时间落到中位数的情况，而这种现象被称为长尾问题。

        > 为了区分是平均的慢还是长尾的慢，最简单的方式就是按照请求延迟的范围进行分组。例如，统计延迟在0-10ms之间的请求数有多少而10-20ms之间的请求数又有多少。通过这种方式可以快速分析系统慢的原因。Histogram和Summary都是为了能够解决这样问题的存在，通过Histogram和Summary类型的监控指标，我们可以快速了解监控样本的分布情况。
  
  4. PromQL
    ①. 范围查询
      > 直接通过类似于PromQL表达式httprequeststotal查询时间序列时，返回值中只会包含该时间序列中的最新的一个样本值，这样的返回结果我们称之为瞬时向量。而相应的这样的表达式称之为--瞬时向量表达式
        - http_requests_total
      
      > 而如果我们想过去一段时间范围内的样本数据时，我们则需要使用区间向量表达式。区间向量表达式和瞬时向量表达式之间的差异在于在区间向量表达式中我们需要定义时间选择的范围，时间范围通过时间范围选择器[]进行定义。
        - http_request_total{}[5m]
      
      > 通过区间向量表达式查询到的结果我们称为区间向量。
      
    ②. 时间位移操作
    在瞬时向量表达式或者区间向量表达式中，都是以当前时间为基准：

    > http_request_total{} # 瞬时向量表达式，选择当前最新的数据
    
    > http_request_total{}[5m] # 区间向量表达式，选择以当前时间为基准，5分钟内的数据
    
    而如果我们想查询，5分钟前的瞬时样本数据，或昨天一天的区间内的样本数据呢? 这个时候我们就可以使用位移操作，位移操作的关键字为offset。

    可以使用offset时间位移操作：

    > http_request_total{} offset 5m
    > http_request_total{}[1d] offset 1d

二. Prometheus 告警处理
---
  - 本章我们将带领读者探索Prometheus的告警处理机制，在前面的部分中已经介绍了告警能力在Prometheus的架构中被划分为两个部分，在Prometheus Server中定义告警规则以及产生告警，Alertmanager组件则用于处理这些由Prometheus产生的告警。Alertmanager即Prometheus体系中告警的统一处理中心。Alertmanager提供了多种内置第三方告警通知方式，同时还提供了对Webhook通知的支持，通过Webhook用户可以完成对告警更多个性化的扩展。
  
  1. Prometheus 的告警处理流程：
  
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LPS9OCY3C6XBe9KTNE4%2F-LPS9QhUbi37E1ZK8mXF%2Fprometheus-alert-artich.png?generation=1540235056680444&alt=media)
  
  2. 告警规则主要有以下部分组成：
    - 告警名称：用户需要为告警规则命名，当然对于命名而言，需要能够直接表达出该告警的主要内容
    
    - 告警规则：告警规则实际上主要由PromQL进行定义，其实际意义是当表达式（PromQL）查询结果持续多长时间（During）后出发告警
  
  3.Alertmanager特性:
  
    - Alertmanager除了提供基本的告警通知能力以外，还主要提供了如：分组、抑制以及静默等告警特性：
    
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LPS9OCY3C6XBe9KTNE4%2F-LPS9QhWQUhAIdLAiEfH%2Falertmanager-features.png?generation=1540235050688808&alt=media)
  
  - 自定义告警规则
    
      groups:
      
      - name: example
      
        rules:
        
        - alert: HighErrorRate
        
          expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
          
          for: 10m
          
          labels:
          
          severity: page
          
        annotations:
        
          summary: High request latency
          
          description: description info
    
    在告警规则文件中，我们可以将一组相关的规则设置定义在一个group下。在每一个group中我们可以定义多个告警规则(rule)。一条告警规则主要由以下几部分组成：

        alert：告警规则的名称。

        expr：基于PromQL表达式告警触发条件，用于计算是否有时间序列满足该条件。

        for：评估等待时间，可选参数。用于表示只有当触发条件持续一段时间后才发送告警。在等待期间新产生告警的状态为pending。

        labels：自定义标签，允许用户指定要附加到告警上的一组附加标签。

        annotations：用于指定一组附加信息，比如用于描述告警详细信息的文字等，annotations的内容在告警产生时会一同作为参数发送到Alertmanager。
    
    1. 为了能够让Prometheus能够启用定义的告警规则，我们需要在Prometheus全局配置文件中通过rule_files指定一组告警规则文件的访问路径，Prometheus启动后会自动扫描这些路径下规则文件中定义的内容，并且根据这些规则计算是否向外部发送通知
    
      rule_files:
    
        [ - <filepath_glob> ... ]
    
    2.       
