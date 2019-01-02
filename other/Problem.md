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
