考核
===
**一. Linux系统下的安装与卸载** 

①. 安装docker CE(ubuntu 和 debain，其他方式参考 [官网](https://docs.docker.com/install/) )：

说明： 官方 docker EE 不支持 debain 安装 。

1. docker ce 之前，要先卸载之前的老版本 docker ，之前的版本被称为 docke.io docker-engine docker 。

> sudo apt-get remove docker docker-engine docker.io containerd runc

2. 安装镜像仓库：

- 更新 update 包

> sudo apt-get update

- 通过 https 用 apt 从仓库中获取 docker 包

>   sudo apt-get install \\
    apt-transport-https \\
    ca-certificates \\
    curl \\
    gnupg-agent \\
    software-properties-common

- 增加 docker 官方的 GPG 秘钥。

> curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

- 验证秘钥 

> sudo apt-key fingerprint 0EBFCD88

结果应该是 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

- 安装 docker-ce 

> sudo apt-get update

> sudo apt-get install docker-ce docker-ce-cli containerd.io

- 安装指定的 docker 版本。先列出仓库中 docker 的版本，在安装。

> apt-cache madison docker-ce

> sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

> sudo systemctl enable docker 
> sudo systemctl start docker 

3. 卸载 docker-ce 

- 卸载软件包

> sudo apt-get purge docker-ce

- 删除所有的images, containers和volumes

> sudo rm -rf /var/lib/docker

②. 安装和卸载 mysql (centos7)

1. 安装 mysql 需要的源包，并且在本地进行安装。

> wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
> yum localinstall mysql57-community-release-el7-8.noarch.rpm

2. 检测是否安装成功

> yum repolist enabled | grep "mysql.*-community.*"

3. 安装 mysql server

> yum install mysql-community-server

4. 设置 mysql 开机自启动，并且启动 mysql

> systemctl enable mysqld
> systemctl start mysqld

卸载 mysql：

- 直接卸载 mysql 

> yum remove mysql

- 使用 rpm 查看，安装中所存在的依赖包

> rpm -qa|grep -i mysql

**如果存在结果，那么就讲所列出的包依次删除，直到没有输出为止**

- 使用 whereis mysql 看是否还有残留

③. 安装和卸载 go sdk 1.11，jdk 8 ，maven 3.3 

- 下载对应的版本的安装包 

> wget (domain)

- 解压对应的安装包：

> tar zxvf (pkg name) -C （location）

- 配置对应的环境变量

> 到 /etc/profile 中增加对应的环境变量。

- 应用 

> source /etc/profile

卸载：

- 删除环境变量的配置
- 删除安装文件夹

**问题：**

1. ”Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host“

解决方案：
在 ansible.config 文件中，修改为 host_key_checking = False  
