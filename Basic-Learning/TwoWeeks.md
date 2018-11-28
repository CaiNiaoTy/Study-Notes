# Study-Notes
> 不要迷失，一步一个脚印。
---
## 一. Linux：
  Linux目录结构为树状结构，最顶级的目录为根目录/。
  - 相对路径：
  
    不由 / 写起，如从/usr/local 到 /usr/share 目录下：cd ../share 其中 .. 表示上层目录， . 表示当前目录。
    
  - 绝对路径：
    
    由根目录 / 写起，如：/usr/local
    
  - 常用命令：
    
    **1**. ls [列出目录]（list）：
    
        1）格式：ls [-参数列表] 目录名称
      
        2）常用参数说明：
        
          > -a: 列出全部的文件，包括隐藏文件都列出来。
          > -l: 按照长数据串列出，包括文章的权限与属性等。
          > -d: 列出目录本身，而不显示其他文件数据。
          
        3）实例：
        
          > ls -al 
          
    **2**. cd [切换目录]（Change Directory）：
    
        1）格式：cd [相对或绝对路径]
        
        2）实例：cd /usr/local
        
    **3**. pwd [显示当前目录]（Print Working Directory）：
        
        1）格式：pwd [-参数列表] 目录名称
          
        2）实例：
        
          > pwd -P
          
    **4**. mkdir [创建新目录]（make directory）：
        
        1）格式：mkdir [-参数列表] 目录名称
        
        2）常用参数说明：
        
          > -m：配置文件的权限！
          > -p：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！ 
          
        3）实例：
        
          > mkdir -m 711 test2
          > mkdir -p test/test1/test2
          
    **5**. rmdir [删除空的目录]（Remove Directory）：
        
        1）格式：rmdir [-参数列表] 目录名称
        
        2）常用参数说明：
        
          > -p：递归删除，连同上一级目录(空的)一起删除。
          
        3）实例：
        
          > rmdir -p test1/test2/test3/test4
          
        注意：rmdir 仅能删除空的目录，你可以使用 rm 命令来删除非空目录。
        
    **6**. rm [移除文件或目录]：
        
        1）格式：rm [-参数列表] 目录/文件名称
        
        2）常用参数说明：
        
          > -f：force，忽略不存在的文件，不会出现警告信息。
          > -i：互动模式，在删除前会询问使用者是否动作。
          > -r：递归删除。
          
        3）实例：
        
          > rm -i bashrc
            rm: remove regular file `bashrc'? y

     **7**. mv [移动文件与目录，或修改名称]（move）：
        
        1）格式：mv [-参数列表] 目录名称
        
        2）常用参数说明：
        
          > -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
          > -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
          > -u ：若目标文件已经存在，且 source 比较新，才会升级 (update)
          
        3）实例：
        
          > mv bashrc mvtest 更名操作
          > mv bashrc test/ 移动文件
          
  - vi/vim:
    
    **1**. vi/vim对应键盘图：

      ![](http://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)

    **2**. 基本上 vi/vim 共分为三种模式，分别是命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）。

        1）命令模式:使用vi/vim此时就是命令模式，i表示的是插入命令，x表示的是删除当前光标所在的位置字符，:表示的是切换到底线命令模式，以在最底一行输入命令。 

        2）输入模式：命令模式使用i,o, a之后就进入了输入模式。

        3）底线命令模式：在命令模式在使用：进入该模式,按ESC退出该模式。

          > q：表示退出程序

          > w：表示保存文件
        
        [更多操作](http://www.runoob.com/linux/linux-vim.html)。     
---
## 二. Linux下配置SSH远程登录：

  - SSH简介：

    **1**. 简单说，SSH是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。最早的时候，互联网通信都是明文通信，一旦被截获，内容就暴露无疑。1995年，芬兰学者Tatu Ylonen设计了SSH协议，将登录信息全部加密，成为互联网安全的一个基本解决方案，迅速在全世界获得推广，目前已经成为Linux系统的标准配置。SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。这里的配置针对的实现是OpenSSH，它是自由软件，应用非常广泛。这里只讨论SSH在Linux Shell中的用法。

    **2**. 中间人攻击：
      
      SSH之所以能够保证安全，原因在于它采用了公钥加密。整个过程是这样的：（1）远程主机收到用户的登录请求，把自己的公钥发给用户。（2）用户使用这个公钥，将登录密码加密后，发送回来。（3）远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。这个过程本身是安全的，但是实施的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，也就是说，都是自己签发的。可以设想，如果攻击者插在用户与远程主机之间（比如在公共的wifi区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就荡然无存了。这种风险就是著名的"中间人攻击"（Man-in-the-middle attack）。

  - 安装OpenSSH客户端/服务器端。

    > sudo apt-get install openssh-client / sudo apt-get install openssh-server。

  - 查看ssh服务的状态

    > sudo service sshd(ssh服务) status

  - 开启ssh服务

    > sudo service sshd start

  - 重启ssh服务

    > sudo service sshd restart

  - [OpenSSH免密码登录](https://blog.csdn.net/wenyun_kang/article/details/77413714)

    免密码登录也就是将公钥进行移植的过程。

    1）创建公钥和密钥：  

      > 执行ssh-keygen  -t  rsa 

    2）将公钥复制到你要免密登录该服务器的节点。

      > ssh-copy-id ip地址

  - 远程登录SSH

    > ssh 用户名@ip地址 或者 ssh 用户名 ip，其中如果当前用户名和服务器的用户名一样可以省略输入ip和密码后可以进行链接。

---
## 三.Linux下配置shadowsocks客户端。
  
  **注意**：所有的操作都要在root用户下进行，不然会出现权限不足的情况，而导致配置失败。

  - 使用python中的pip命令来进行安装，此时的python版本为3才能够进行成功安装shadowsocks。

    > apt install python-pip

    > pip install shadowsocks

  - 在/etc下配置shadowsocks.json文件。文件内容为服务器端的内容！
  
    ｛
      “server“:“192.xxx.xxx.xxx“,

      “server_port“:xxxx,

      “local_address“: “127.0.0.1“,

      “local_port“:9050,

      “password“:“xxxxxxxxxxxxx“,

      “timeout“:300,

      “method“:“aes-256-cfb“

      }

  - 使用如下命令启动shadowsocks。

    > nohup sslocal -c /etc/shadowsocks.json /dev/null 2>&1 &

    说明：使用的是sslocal这个命令，表示shadowsocks以客户端模式工作，前面的nohub 和最后的 & 表示后台执行且关闭 session 后仍然在后台执行，否则将会阻塞shell端口.

  - 安装Privoxy

    > apt install privoxy -y

    上述安好了shadowsocks，但它是socks5代理，我门在shell里执行的命令，发起的网络请求现在还不支持socks5代理，只支持http/https代理。为了我门需要安装privoxy代理，它能把电脑上所有http请求转发给shadowsocks.

  - 配置代理

    1）查看vim /usr/local/etc/privoxy/config文件，

    2）先搜索关键字listen-address找到listen-address 127.0.0.1:8118这一句，保证这一句没有注释，8118就是将来http代理要输入的端口。

    3）然后搜索forward-socks5t, 将#forward-socks5t / 127.0.0.1:9050 .此句前面的注释去掉, 意思是转发流量到本地的9050端口, 而9050端口正是 ss 监听的端口

  - 启动Privoxy

    > systemctl restart privoxy

    > systemctl enable privoxy

  - 转发配置

    1）在配置文件中设置环境变量vim /etc/profile

      > export http_proxy=http://127.0.0.1:8118

      > export https_proxy=http://127.0.0.1:8118

    2）source/etc/profile(执行该文件)

  - 测试代理是否成功。

    > curl www.google.com

    响应了说明配置成功。

    **补充**：在浏览器中使用系统代理模式进行上网的时候，要对本地的9050和8118代理端口进行检测看端口是否被监听。

---
## 四.Linux下配置golang语言环境。

  - 下载对应的开发包，[官网](https://golang.google.cn/dl/)。

  - 将下载的二进制包解压至 /usr/local目录，并创建go的工作目录。

      > tar -C /usr/local -xzf go1.4.linux-amd64.tar.gz

      > mkdir /gowork 

  - 设置环境变量：

      > vim /etc/profile

      > export GOROOT=/usr/local/go

      > export GOBIN=$GOROOT/bin

      > export PAHT=$PATH:$GOBIN

      > export GOPATH=/gowork

  - 执行文件，并验证。

      > source /etc/profile

      > go version 

  - 配置beego框架：
      
      1）下载beego
        
        > go get github.com/astaxie/beego
        
        > go get github.com/beego/bee
        
      2）配置环境变量
      
        在工作目录下找到 /bin 目录查看是否存在 bee 可执行文件，若不存在则复制 bee 到该目录下然后配置相应的环境变量
        
        > vim /etc/profile 
        
        > export PATH=$PATH:$GOPATH/bin

---        
## 五.Docker容器：
  
  - 启动容器：

    > docker run ubuntu:15.10 /bin/echo "hello world"

    参数解析：

      1）run：与前面的docker组合来运行一个容器。

      2）ubuntu:15.10：指定要运行的镜像，先从本机的镜像开始查找，如果不存在,Docker就会从镜像仓库Dokcer Hub下载公共的镜像。

        3. /bin/echo  "hello world"：在启动的容器里执行的命令。

  - 运行交互式的容器：

      > docker run -t -i ubuntu:15.10 /bin/bash

      - 参数解析：

        1）-t（preudo-tty）：在新容器内指定一个伪终端。

        2）-i：允许你对容器内的标准输入（STDIN）进行交互。

        3）通过CTRL+D或者exit退出当前容器。

  - 以后台模式启动容器：

      > docker run -d ubuntu:15.10 

      注意：容器是否会长久运行，和docker run指定的命令有关，与-d 参数无关。

  - 查看容器的状态：

      > docker container ls -a （查看所有状态的容器）

      > docker ps （查看运行状态的容器）

  - 停止容器，关闭容器和启动停止的容器，清理所有处于终止状态的容器：

      > docker container stop <container ID | name> 

      > docker container rm <container ID | name> 

      > docker container start/restart <container ID | name>

      > docker container  prune

  - 进入容器：

      1）使用docker attach进入：

        > docker attach <container ID | name> 

        如果使用exit，会导致容器的停止

      2）使用docker exec进入（推荐使用）后面可以跟多个参数 主要使用 -i -t：

        > docker exec -it <container ID | name> bash

        使用exit退出，不会导致容器停止。

      3）-p和-P参数说明：

        -p：是指定容器内端口和宿主机端口进行网络映射。

        -P：容器内部使用随机端口映射到宿主机上。

        > 查看容器端口的快捷方式：docker port <container ID | name>

  - 查看容器内运行的进程：

    > docker top <container ID | name>

  - 查看Docker的底层信息：

    > docker inspect <container ID | name>

---
## 六.Git

  - Git的工作流程：
    
    1）克隆Git资源作为工作目录
    
    2）在克隆的资源上增加或修改文件
       
    3）如果其他人修改了，你可以更新资源。

    4）在提交之前查看修改。
    
    5）提交修改
    
    6）在修改完成后，如果发现错误，可以测绘提交并修改后再次提交。
  
  - Git的工作区、暂存区和版本库。
  
    1）工作区：本机上的目录。
    
    2）暂存区：一般放在.git目录下的index文件中，英文名称（stage/index），有时候也称作为索引（index）。
   
    3）版本库：工作区的隐藏目录.git，是Git的版本库。

  - Git的相关操作
  
    1）初始化当前目录为作为git创库：
    
      > git init
    
    2）克隆Git仓库中的内容到本地:
    
      > git clone [repo] [directory]   
   
    3）增加文件到暂存区：
          
      > git add [filename]
      
    4）查看上次提交之后是否有修改：
            
      > git status -s(简短的结果输出)
      
    5）取消已缓存的内容：
              
      > git reset HEAD
      
    6）移除某个文件：
            
      > git rm <file>    
      
    7）强制删除：
              
      > git rm -f <file>   
      
    8）从缓存中删除：
              
      > git rm --cached <file>   
      
    8）递归删除目录中的所有东西：
              
      > git rm -r *   
      
    9）移动或重命名一个文件、目录、软连接：
            
      > git mv <before filename>  <new filename>    
          
    10）提交到本地仓库中：
            
      > git commit -m "描述信息"    
        
    11）查看当前仓库所有的分支：
              
      > git branch <分支名>    
      
    12）创建新的分支：
              
      > git branch <分支名>  
      
    13）切换分支：
              
      > it checkout <分支名> [-b]表示创建分支后立即切换到该分支下        
      
    14）删除分支：
            
      > git branch -d <分支名>
      
    15）合并分支：
              
      > git merge <分支名> 
          
          
    ![](https://7n.w3cschool.cn/attachments/image/20170206/1486348362884912.jpg)
   
   
---
## 七.Kubernetes

  - 简介：
    
    Kubernetes之间有8个字符，所以简写为K8S。k8s是google开源的容器集群管理系统。在Docker基础上为容器化的应用提供部署运行、资源调度。服务发现和动态伸缩灯一系列完整功能，提高了大规模容器集群管理的便捷性。
    
  - 总体架构：
       
    ![](images.gitbook.cn/99c25530-2feb-11e8-83f6-cd5ce302e425)

  - 基本概念：
    
    1）Pod：K8s的基本运行单元。
  
    2）ReplicaSet：Pod的集合。
    
    3）Deployment：提供更新支持。
  
    4）StatefulSets：提供有状态支持。
    
    5）Volume：数据卷。
    
    6）Lables：标签，资源间的关联一般通过这个来实现。
   
  - kubernetes基本命令：
  
    1）在master创建一个部署到k8s集群上,使用以下命令：
    
      > kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
      
      参数说明：kubernetes-bootcamp（部署<deployments>的名称），image（镜像），port（运行该应用指定的端口号）。
    
    2）查看部署的状态：
    
      > kubectl get deployments
    
    3）在Kubernetes中运行的Pod正在一个私密的隔离网络上运行。 默认情况下，它们可以从同一kubernetes集群中的其他pod和服务中看到，但不能在该网络之外。当我们使用kubectl时，我们通过API端点进行交互以与我们的应用程序进行通信。
    
      > kubectl proxy
    
    4）设置相应的环境变量进行访问：
    
      > export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    
    5）查看相关Pod的信息,查看Pod内的详细信息，查看Pod的日志信息： 
    
      > kubectl get pods
            
      > kubectl describe pods
      
      > kubectl log Pod名称
      
    6）Pod运行后我们可以直接在容器上执行相关命令： 
    
      > kubectl exec Pod名称 env

      > kubectl exec -i -t Pod名称 bash

  - 使用服务提供外部访问：
  
    > kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port=8080
    
    说明：deployment 是你所部署在集群内的名叫 kubernetes-bootcamp 的一个应用 ，type 是指定服务类型 port 是端口号。然后通过 get services 来查看其中的服务所暴露的端口号，通过设置环境变量来进行外部访问服务。
    
    > kubectl get services
    
    > export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
    
  - 使用标签：
  
    1）查看默认的pod中的标签值。
      
      > kubectl describe deployment 
    
    2）查看相应的pod和服务。
      
      > kubectl get pods -l run=kubernetes-bootcamp
    
      > kubectl get services -l run=kubernetes-bootcamp
      
    3）给pod节点增加标签（标签是键值对的类型）
    
      > kubectl label pod <podname> app=v1
  
  - 删除服务：
  
    1）使用标签进行删除：
      
      > kubectl delete service -l run=kubernetes-bootcamp
  
  - 扩展应用：
  
    1）deployment的状态描述：
    
     > kubectl get deployment 
     
     其中 DESIRED 表示的已经配置的副本的数量。

     CURRENT 表示的是正在运行的副本数量。

     UP-TO-DATE 表示的是已更新以匹配所需（已配置）状态的副本数

     AVAILABLE 显示实际可用于用户的副本数量

    2）扩展Deployment副本的数量：
    
     > kubectl scale deployments/kubernetes-bootcamp --replicas=4

     其中 --replicas 表示的是副本的数量。   
   
    3）负载均衡：
      
     可以用如下命令查看服务是否已经对流量进行了负载均衡，如果服务进行了负载均衡，我们可以通过服务暴露的端口号进行访问
      
     > kubectl  describe deployments/kubernetes-bootcamp
      
    4）缩小副本：
    
     > kubectl scale deployments/kubernetes-bootcamp --replicas=2
      
  - 更新应用并发布更新到集群：
  
    1）更新镜像：
    
      > kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
      
      此时分配的pod是全新的pod，而旧的pod已经被停止了。
      
    2）确认更新：
      
      > kubectl rollout status deployments/kubernetes-bootcamp
      
    3）回退更新之前的版本：
        
      > kubectl rollout undo deployments/kubernetes-bootcamp
      
      
     
