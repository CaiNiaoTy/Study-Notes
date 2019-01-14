一 Grafana 基本简介
--- 

**1. 数据源:** 
  
  > 对于Grafana而言，Prometheus这类为其提供数据的对象均称为数据源（Data Source）。目前，Grafana官方提供了对：Graphite, InfluxDB, OpenTSDB, Prometheus, Elasticsearch, CloudWatch的支持。对于Grafana管理员而言，只需要将这些对象以数据源的形式添加到Grafana中，Grafana便可以轻松的实现对这些数据的可视化工作。
  
**2. Dashboards:**

  > 在Grafana中，通过Dashboard来组织和管理我们的数据可视化图表。
  
  ![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBdoxo9EmQ0bJP2BuUi%2F-LRX7f16nDH_eSWU4Szc%2F-LRX7gzvWm8jDHg4S9fc%2Fdashboard-components.png?generation=1542465969279644&alt=media)
  
  在一个Dashboard中一个最基本的可视化单元为一个Panel（面板），Panel通过如趋势图，热力图的形式展示可视化数据。 并且在Dashboard中每一个Panel是一个完全独立的部分，通过Panel的Query Editor（查询编辑器）我们可以为每一个Panel自己查询的数据源以及数据查询方式。
  
  例如，如果以Prometheus作为数据源，那在Query Editor中，我们实际上使用的是PromQL，而Panel则会负责从特定的Prometheus中查询出相应的数据，并且将其可视化。由于每个Panel是完全独立的，因此在一个Dashboard中，往往可能会包含来自多个Data Source的数据。
  
  在Dashboard页面中，还可以定义一个Row（行），来组织和管理一组相关的Panel。  
  
  除了Panel, Row这些对象以外，Grafana还允许用户为Dashboard定义Templating variables（模板参数），从而实现可以与用户动态交互的Dashboard页面。同时Grafana通过JSON数据结构管理了整个Dasboard的定义，因此这些Dashboard也是非常方便进行共享的。Grafana还专门为Dashboard提供了一个共享服务：https://grafana.com/dashboards
  通过该服务用户可以轻松实现Dashboard的共享，同时我们也能快速的从中找到我们希望的Dashboard实现，并导入到自己的Grafana中。
