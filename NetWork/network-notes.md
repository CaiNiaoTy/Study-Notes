# 一.计算机网络的简介及基础知识
- **计算机网络的概述**
 
  **1. 计算机网络的功能**
  
   * 连通性：让计算机网络上的用户能够进行信息交换
   * 共享：资源共享
   
  **2. 网络中的三网**
  
   * 电信网络，有线电视网络，计算机网络
  
  **3. 网络的组成**
   
   * 网路由若干结点（计算机、路由器或交换机、集线器等设备）和连接这些结点的链路组成。
   * 网络和网络之间通过路由器连接起来组成就组成了互联网，在网络上的计算机称之为主机（host），因特网是最大的互联网。
   
  **4. 因特网的组成**
   
   * 1）边缘部分：所有连在因特网上的主机组成
   * 2）核心部分：由大量网络和连接这些网络的路由器组成，这部分是为边缘部分提供服务的（连通性和交换）
  
  **5. 计算机之间的通信**
   
   * 指的是两个主机之间的进程之间进行通信
   * 通信的方式分为两种：
    
     - 客户端服务器：一方进行发送请求，一方进行处理请求
     - 对等方式：发送方也可以作为处理方
     
  **6. 路由器**
   
   * 在网络核心部分其特殊作用的就是路由器，是实现分组交换的关键构建，其任务是转发收到的分组。
   * 分组转发：
     - 采用的是存储转发技术，发送的是一个分组。
     - 通常把发送的整块数据成为一个报文（message），在发送报文之前会把该报文划分为更小的等长的数据段，在每个数据段之前加上一些必要的控制信息组成的首部后，就构成了一个分组。也称为“包”
     - 分组是在因特网上传输的数据单元。首部包含了目的地址和源地址等重要信息。
     - 路由器在接收到分组后，先暂存在存储器（内存）中，在检查首部，查找转发表，按照首部中的目的地址，找到合适的端口在转发出去，把分组交给下个路由器。
  

- **计算机网络的体系结构**

  **1. OSI/RM（Open Systems Interconnection Reference Model）开放系统互联基本参考模型**
  
   * 上面的参照模型是七层参考模型，而现在使用的是TCP/IP标准。
  
  **2. 网络协议**
   
   * 定义：为进行网络中的数据交换而建立的规则、标准或约定称为网络协议。
   * 三要素：1）语法、2）语义、3）同步
  
  **3. 定义** 
   
   * 计算机网络的各层及其协议的集合，称为网络的体系结构。
  
  **4. 五层体系结构**
   
   * OSI是七层体系结构，TCP/IP是四层体系结构。五层协议只是为介绍网咯原理而设计的，实际应用时TCP/IP四层协议
   
   * 结构及其功能：
    
     - 应用层：为用户的应用进程提供服务
     - 运输层：负责向两个进程之间的通信提供服务
     - 网络层：为分组交换网上的不同主机提供通信服务
     - 数据链路层：在两个主机之间的链路上传输数据
     - 物理层：透明的传送比特流，传送的就是比特

# 二.物理层
  
  **1. 主要作用**
   
   * 物理层重点考虑是怎样在连接的计算机上的各种传输媒介上传输比特流。
  
  **2. 数据通讯的基础知识**
  
   * 数据通讯系统的模型：
     
     - 源系统：源点和发送器
     - 传输系统：网络或传输线
     - 目的系统：接收器和终点
   
   * 通信的目的是传输消息，消息的载体是数据，信号是数据的电气或电磁表现形式。
   
   * 信号分类：
     
     - 模拟信号：
     - 数字信号：
    
   
   
