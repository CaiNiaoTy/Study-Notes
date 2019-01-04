2019.1.2
---

一. 进制转换。
------

  > 其他进制转换为十进制，将每一位的基础上乘以对应的次方！例如，0x15（16进制）表示的十进制为 21 ，其中 0x 表示的是十六进制。

二. docker images 本地迁移再推送到远程仓库。
--- 

  ①. 现在本地打上对应的标签
  > docker tag [images ID|images name] [label name]
  
  ②. 在本地将镜像保存为文件
  > docker save image_name -o file_path
  > 其中 file_path 是导出 tar 文件路径。
  
  ③. 将 image tar 文件传到其他机器
  > scp [local directory path] [user name]@[server name]:[server directory path]
  
  ④. 在获取到文件的机器上使用命令加载 image tar 文件
  > docker load -i file_path
  
  ⑤. 将镜像推送到远程registry
  > docker push [image name] 

三. Linux 下 scp 的用法
--- 

  scp 是 Secure copy 的简写，表示是 Linux 下远程拷贝的命令，scp 传输是加密的。
  
  ①. 将远程服务器的文件或目录拷贝到本地
  > scp [-r] [user name]@[server name]:[server directory[file] path] [local directory path]
  
  ②. 将本地文件或目录拷贝到远程服务器的目录中
  > scp [-r] [local directory path] [user name]@[server name]:[server directory path]

四. Linux 下的挂载
--- 
  > 先解释分区，分区指的是将硬盘进行分隔，是硬件层面！所谓挂载就是在windows下进行“分区”操作，而再 linux 系统下则是将‘分区’和对应的目录惊醒绑定操作。
  
  > 盘符——是软件级的概念；分区——是硬件级的概念；这个“分配盘符”的过程，就是挂载（mount）过程（请一定记住这个mount）

2019.1.4
---
  
一. SMTP
---
  1. 简单邮件传输协议 (Simple Mail Transfer Protocol, SMTP) 是在Internet传输email的事实标准。

  2. SMTP是一个相对简单的基于文本的协议。在其之上指定了一条消息的一个或多个接收者（在大多数情况下被确认是存在的），然后消息文本会被传输。可以很简单地通过telnet程序来测试一个SMTP服务器。SMTP使用TCP端口25。要为一个给定的域名决定一个SMTP服务器，需要使用MX (Mail eXchange) DNS。
  
二. Webhook
---
  1. Webhook，也就是人们常说的钩子，是一个很有用的工具。你可以通过定制 Webhook 来监测你在 Github.com 上的各种事件，最常见的莫过于 push 事件。如果你设置了一个监测 push 事件的 Webhook，那么每当你的这个项目有了任何提交，这个 Webhook 都会被触发，这时 Github 就会发送一个 HTTP POST 请求到你配置好的地址。
