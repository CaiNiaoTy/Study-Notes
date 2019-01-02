2019.1.2
---

一. Kubernetes 安装注意事项
---
  ①. 
 
二. Pod 
---
  1. Kubernetes 中对长时间运行容器的要求是：主程序一直在前台运行，如果创建 docker 镜像的启动命令是后台执行程序，则 kubelet 创建完成包含这个容器的 Pod之后，会认为 Pod 执行结束了，如果在 RC 中定义了 Pod 的副本数则会一直执行这样的操作，直到达到预期目标。
  
  2. 静态 Pod 是有 kubelet 进行管理的仅仅存在于特定 Node 上的 Pod。它们不能通过 API Server 进行管理，无法与 ReplicationCotrolle、Deployment DaemonSet 进行关联，并且 kubelet 无法对他们进行健康检查。静态 Pod 有kubelet进行创建，并且总是在 kubelet 所在 Pod 上运行
    
    - 创建静态 Pod 有两种方式：配置文件或者 HTTP 方式。
  
  3. Pod 容器共享 Volume
    
    - 
    
