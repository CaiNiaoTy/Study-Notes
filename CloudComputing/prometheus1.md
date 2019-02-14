Prometheus 中的服务发现与relabel 
---
**1. Relabel 机制：**

①. 默认情况下，当Prometheus加载Target实例完成后，这些Target时候都会包含一些默认的标签：

- _ _ address _ _：当前Target实例的访问地址<host>:<port>
- _ _ scheme _ _：采集目标服务访问地址的HTTP Scheme，HTTP或者HTTPS
- _ _ metrics_path _ _：采集目标服务访问地址的访问路径
- _ _ param_<name>：采集任务目标服务的中包含的请求参数

②. 说明： 

> 一般来说，Target以__作为前置的标签是在系统内部使用的，因此这些标签不会被写入到样本数据中。不过这里有一些例外，例如，我们会发现所有通过Prometheus采集的样本数据中都会包含一个名为instance的标签，该标签的内容对应到Target实例的__address__。 这里实际上是发生了一次标签的重写处理。

> 这种发生在采集样本数据之前，对Target实例的标签进行重写的机制在Prometheus被称为Relabeling。
