# Docker

+++

## 一、基础篇

### 1 Docker简介

#### 1.1 docker是什么

> 问题：为什么会有docker出现？

假定您在开发一个尚硅谷的谷粒商城，您使用的是一台笔记本电脑而且您的开发环境具有特定的配置。其他开发人员身处的环境配置也各有不同。您正在开发的应用依赖于您当前的配置且还要依赖于某些配置文件。此外，您的企业还拥有标准化的测试和生产环境，且具有自身的配置和一系列支持文件。您希望尽可能多在本地模拟这些环境而不产生重新创建服务器环境的开销。请问？

您要如何确保应用能够在这些环境中运行和通过质量检测？并且在部署过程中不出现令人头疼的版本、配置问题，也无需重新编写代码和进行故障修复？

答案就是使用容器。Docker之所以发展如此迅速，也是因为它对此给出了一个标准化的解决方案-----**系统平滑移植，容器虚拟化技术**。

环境配置相当麻烦，换一台机器，就要重来一次，费力费时。很多人想到，能不能从根本上解决问题，**软件可以带环境安装？**也就是说，**安装的时候，把原始环境一模一样地复制过来。开发人员利用 Docker 可以消除协作编码时“在我的机器上可正常工作”的问题**。

![image-20221203193827010](07-docker.assets/image-20221203193827010.png)

之前在服务器配置一个应用的运行环境，要安装各种软件，就拿尚硅谷电商项目的环境来说，Java/RabbitMQ/MySQL/JDBC驱动包等。安装和配置这些东西有多麻烦就不说了，它还不能跨平台。假如我们是在 Windows 上安装的这些环境，到了 Linux 又得重新装。况且就算不跨操作系统，换另一台同样操作系统的服务器，要**移植**应用也是非常麻烦的。

传统上认为，软件编码开发/测试结束后，所产出的成果即是程序或是能够编译执行的二进制字节码等(java为例)。而为了让这些程序可以顺利执行，开发团队也得准备完整的部署文件，让维运团队得以部署应用程式，<font color="gree">开发需要清楚的告诉运维部署团队，用的全部配置文件+所有软件环境。不过，即便如此，仍然常常发生部署失败的状况</font>。Docker的出现<font color="red">使得Docker得以打破过去「程序即应用」的观念。透过镜像(images)将作业系统核心除外，运作应用程式所需要的系统环境，由下而上打包，达到应用程式跨平台间的无缝接轨运作</font>。

> docker理念

**Docker是基于Go语言实现的云开源项目**。

Docker的主要目标是“Build，Ship and Run Any App,Anywhere”，也就是通过对应用组件的封装、分发、部署、运行等生命周期的管理，使用户的APP（可以是一个WEB应用或数据库应用等等）及其运行环境能够做到“**一次镜像，处处运行**”。

![image-20221203194010893](07-docker.assets/image-20221203194010893.png)

<font color="gree">Linux容器技术的出现就解决了这样一个问题，而 Docker 就是在它的基础上发展过来的</font>。将应用打成镜像，通过镜像成为运行在Docker容器上面的实例，而 Docker 容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。<font color="red">只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作</font>。

> 总结：
>
> 解决了**运行环境和配置问题**的**软件容器**，方便做持续集成并有助于整体发布的容器虚拟化技术。



#### 1.2 容器与虚拟机比较

> 传统虚拟机技术

虚拟机（virtual machine）就是带环境安装的一种解决方案。

它可以在一种操作系统里面运行另一种操作系统，比如在Windows10系统里面运行Linux系统CentOS7。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变。  

| Win10 | VMWare | Centos7 | 各种cpu、内存网络额配置+各种软件 | 虚拟机实例 |
| ----- | ------ | ------- | -------------------------------- | ---------- |

传统虚拟机技术基于安装在主操作系统上的虚拟机管理系统(如: VirtualBox 和
VMWare等)，创建虚拟机(虚拟出各种硬件)，在虚拟机上安装从操作系统，在从操作系统中安装部署各种应用。

![image-20221203194945475](07-docker.assets/image-20221203194945475.png)

虚拟机的缺点：

1. 资源占用多
2. 冗余步骤多
3. 启动慢

> 容器虚拟化技术

由于前面虚拟机存在某些缺点，Linux发展出了另一种虚拟化技术：<font color="gree">Linux容器(Linux Containers，缩写为 LXC)</font>。

Linux容器是与系统其他部分隔离开的一系列进程，从另一个镜像运行，并由该镜像提供支持进程所需的全部文件。容器提供的镜像包含了应用的所有依赖项，因而在从开发到测试再到生产的整个过程中，它都具有可移植性和一致性。

**Linux 容器不是模拟一个完整的操作系统**而是对进程进行隔离。有了容器，就可以将软件运行所需的所有资源打包到一个隔离的容器中。<font color="gree">容器与虚拟机不同，不需要捆绑一整套操作系统</font>，只需要软件工作所需的库资源和设置。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行。

![image-20221203195326305](07-docker.assets/image-20221203195326305.png)

Docker容器是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统虚拟机则是在硬件层面实现虚拟化。与传统的虚拟机相比，Docker 优势体现为启动速度快、占用体积小。

> 对比

![image-20221203195605687](07-docker.assets/image-20221203195605687.png)

比较了 Docker 和传统虚拟化方式的不同之处：

* 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；
* 容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

* 每个容器之间互相隔离，每个容器有自己的文件系统，容器之间进程不会相互影响，能区分计算资源。

![img](07-docker.assets/1652093324480-073374a2-4ce3-4a56-932b-5a73329736a2.jpeg)

对比：

| **特性**         | **容器**           | **虚拟机** |
| ---------------- | ------------------ | ---------- |
| **启动**         | 秒级               | 分钟级     |
| **大小**         | 一般为Mb           | 一般为Gb   |
| **速度**         | 接近原生           | 比较慢     |
| **系统支持数量** | 单机支持上千个容器 | 一般几十个 |



#### 1.3 docker能干嘛？

> 技术职级变化

- coder
- programmer
- software engineer
- DevOps engineer

> 开发/运维（DevOps）新一代开发工程师

一次构建，随处运行：

1. 更快速的应用交付和部署

   传统的应用开发完成后，需要提供一堆安装程序和配置说明文档，安装部署后需根据配置文档进行繁杂的配置才能正常运行。Docker化之后只需要交付少量容器镜像文件，在正式生产环境加载镜像并运行即可，应用安装配置在镜像里已经内置好，大大节省部署配置和测试验证时间。

2. 更便捷的升级和扩缩容

   随着微服务架构和Docker的发展，大量的应用会通过微服务方式架构，应用的开发构建将变成搭乐高积木一样，每个Docker容器将变成一块“积木”，应用的升级将变得非常容易。当现有的容器不足以支撑业务处理时，可通过镜像运行新的容器进行快速扩容，使应用系统的扩容从原先的天级变成分钟级甚至秒级。

3. 更简单的系统运维

   应用容器化运行后，生产环境运行的应用可与开发、测试环境的应用高度一致，容器会将应用程序相关的环境和状态完全封装起来，不会因为底层基础架构和操作系统的不一致性给应用带来影响，产生新的BUG。当出现程序异常时，也可以通过测试环境的相同容器进行快速定位和修复。

4. 更高效的计算资源利用

   Docker是*内核级虚拟化*，其不像传统的虚拟化技术一样需要额外的Hypervisor支持，所以在一台物理机上可以运行很多个容器实例，可大大提升物理服务器的CPU和内存的利用率。

> Docker应用场景

![image-20221203200316193](07-docker.assets/image-20221203200316193.png)



#### 1.4 下载地址

docker官网：http://www.docker.com

Docker Hub官网: https://hub.docker.com/



### 2 Docker安装

#### 2.1 前提说明

> CentOS Docker 安装

![image-20221203200809223](07-docker.assets/image-20221203200809223.png)

> 前提条件

目前，CentOS 仅发行版本中的内核支持 Docker。Docker 运行在CentOS 7 (64-bit)上，

要求系统为64位、Linux系统内核版本为 3.8以上，这里选用Centos7.x

> 查看自己的内核

uname命令用于打印当前系统相关信息（内核版本号、硬件架构、主机名称和操作系统类型等）。

![image-20221203201101183](07-docker.assets/image-20221203201101183.png)



#### 2.2 docker的基本组成

> docker三剑客
>
> 1. 镜像(image)
> 2. 容器(container)
> 3. 仓库(repository)

详细说明：

1. 镜像(image)

   Docker镜像（Image）就是一个**只读**的模板。镜像可以用来创建Docker容器，**一个镜像可以创建很多容器**。

   它也相当于是一个root文件系统。比如官方镜像centos:7就包含了完整的一套centos:7最小系统的root文件系统。

   相当于容器的“源代码”，**docker镜像文件类似于Java的类模板，而docker容器实例类似于java中new出来的实例对象**。

   | **Docker** | **面向对象** |
   | ---------- | ------------ |
   | 容器       | 对象         |
   | 镜像       | 类           |

2. 容器(container)

   - 从面向对象角度

     Docker 利用容器（Container）独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，**容器是用镜像创建的运行实例**。就像是Java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

   - 从镜像容器角度

     **可以把容器看做是一个简易版的 Linux 环境**（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

3. 仓库(repository)

   仓库（Repository）是**集中存放镜像文件**的场所。

   类似于

   Maven仓库，存放各种jar包的地方；

   github仓库，存放各种git项目的地方；

   Docker公司提供的官方registry被称为Docker Hub，存放各种镜像模板的地方。

   仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

   最大的公开仓库是Docker Hub(https://hub.docker.com/)，存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云等

> 小总结：

<font color="gree">需要正确的理解仓库/镜像/容器这几个概念</font>:

Docker本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。

image文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

- 镜像文件

  image 文件生成的容器实例，本身也是一个文件，称为镜像文件。

- 容器实例

  一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器

- 仓库

  就是放一堆镜像的地方，我们可以把镜像发布到仓库中，需要的时候再从仓库中拉下来就可以了



#### 2.3 Docker平台架构图解

> Docker平台架构图解(入门版)

![architecture.svg](https://cdn.nlark.com/yuque/0/2022/svg/12911942/1652093339897-20255a0a-e981-43e3-9e9e-654b8da3b2c8.svg)

Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上，然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。<font color="gree">容器，是一个运行时环境，就是我们前面说到的集装箱。可以对比mysql演示对比讲解</font>。

![image-20221203202656631](07-docker.assets/image-20221203202656631.png)

> Docker平台架构图解(架构版)

首次懵逼正常，后续深入，先有大概轮廓，混个眼熟

整体架构及底层通信原理简述：

Docker 是一个 C/S 模式的架构，后端是一个松耦合架构，众多模块各司其职。

Docker运行的基本流程为：

1. 用户是使用 Docker Client 与 Docker Daemon 建立通信，并发送请求给后者
2. Docker Daemon 作为 Docker 架构的主体部分，首先提供 Docker Server 的功能使其可以接收 Docker Client 的请求
3. Docker Engine 执行 Docker 内部的一系列工作，每一项工作都是以一个 Job 的形式存在
4. Job 的运行过程中，当需要容器镜像时，则从 Docker Registry 中下载镜像，并通过镜像管理驱动 Graph Driver 将下载镜像以 Graph 的形式存储
5. 当需要为 Docker 创建网络环境时，通过网络管理驱动 Network driver 创建并配置 Docker 容器网络环境
6. 当需要限制 Docker 容器运行资源或执行用户指令等操作时，则通过 Exec driver 来完成
7. Libcontainer 是一项独立的容器管理包，Network driver 以及 Exec driver 都是通过 Libcontainer 来实现具体对容器进行的操作

![img](07-docker.assets/1652093347909-4fcf65d1-da12-47cb-9a2f-0c4528d7e4c9.png)



#### 2.4 docker安装步骤

> CentOS7安装Docker
>
> https://docs.docker.com/engine/install/centos/

1. 确定你是CentOS7及以上版本

2. 卸载旧版本

   ![image-20221203204008457](07-docker.assets/image-20221203204008457.png)

3. yum安装gcc相关

   要求CentOS7能上外网

   ```bash
   yum -y install gcc
   
   yum -y install gcc-c++
   ```

   以上两行代码依次执行。

4. 安装需要的软件包

   ![image-20221203204333492](07-docker.assets/image-20221203204333492.png)

   ```bash
   yum install -y yum-utils
   ```

5. 设置stable镜像仓库

   - 大坑

     ```bash
     yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
     ```

     连接的是外网服务器仓库，往往会出现连接慢或连接超时的问题。

   - 推荐

     推荐连接的是国内的阿里云服务器仓库。

     ```bash
     yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
     ```

     ![image-20221203204954167](07-docker.assets/image-20221203204954167.png)

6. 更新yum软件包索引

   ```bash
   yum makecache fast
   ```

7. 安装 DOCKER CE

   ```bash
   yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

8. 启动docker

   ```bash
   systemctl start docker
   ```

9. 测试

   ```bash
   [root@linux-docker ~]# docker version
   Client: Docker Engine - Community
    Version:           20.10.21
    API version:       1.41
    Go version:        go1.18.7
    Git commit:        baeda1f
    Built:             Tue Oct 25 18:04:24 2022
    OS/Arch:           linux/amd64
    Context:           default
    Experimental:      true
   
   Server: Docker Engine - Community
    Engine:
     Version:          20.10.21
     API version:      1.41 (minimum version 1.12)
     Go version:       go1.18.7
     Git commit:       3056208
     Built:            Tue Oct 25 18:02:38 2022
     OS/Arch:          linux/amd64
     Experimental:     false
    containerd:
     Version:          1.6.10
     GitCommit:        770bd0108c32f3fb5c73ae1264f7e503fe7b2661
    runc:
     Version:          1.1.4
     GitCommit:        v1.1.4-0-g5fd4c4d
    docker-init:
     Version:          0.19.0
     GitCommit:        de40ad0
   ```

   ```bash
   [root@linux-docker ~]# docker run hello-world
   
   Hello from Docker!
   This message shows that your installation appears to be working correctly.
   
   To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
       (amd64)
    3. The Docker daemon created a new container from that image which runs the
       executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
       to your terminal.
   
   To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash
   
   Share images, automate workflows, and more with a free Docker ID:
    https://hub.docker.com/
   
   For more examples and ideas, visit:
    https://docs.docker.com/get-started/
   ```

10. 卸载

    ```bash
    systemctl stop docker 
    
    yum remove docker-ce docker-ce-cli containerd.io
    
    rm -rf /var/lib/docker
    
    rm -rf /var/lib/containerd
    ```



#### 2.5 阿里云镜像加速

1. 阿里云官网：https://promotion.aliyun.com/ntms/act/kubernetes.html

2. 注册一个属于自己的阿里云账户(可复用淘宝账号)

3. 获得加速器地址连接

   - 登陆阿里云开发者平台

     ![image-20221203205746912](07-docker.assets/image-20221203205746912.png)

   - 点击控制台

     ![image-20221203205804407](07-docker.assets/image-20221203205804407.png)

   - 选择容器镜像服务

     ![image-20221203205824040](07-docker.assets/image-20221203205824040.png)

   - 获取加速器地址

     ![image-20221203205845440](07-docker.assets/image-20221203205845440.png)

4. 粘贴脚本直接执行

   ![image-20221203205939509](07-docker.assets/image-20221203205939509.png)

5. 重启服务器

   ```bash
   systemctl daemon-reload
   
   systemctl restart docker
   ```



#### 2.6 永远的HelloWorld

> 启动Docker后台容器(测试运行 hello-world)

![image-20221203210112752](07-docker.assets/image-20221203210112752.png)

输出这段提示以后，hello world就会停止运行，容器自动终止。

> run干了什么

![image-20221203210203319](07-docker.assets/image-20221203210203319.png)



#### 2.7 底层原理

> 为什么Docker会比VM虚拟机快？

1. docker有着比虚拟机更少的抽象层

   由于docker不需要Hypervisor(虚拟机)实现硬件资源虚拟化，运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。

2. docker利用的是宿主机的内核，而不需要加载操作系统OS内核

   当新建一个容器时，docker不需要和虚拟机一样重新加载一个操作系统内核。进而避免引寻、加载操作系统内核返回等比较费时费资源的过程，当新建一个虚拟机时，虚拟机软件需要加载OS，返回新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统，则省略了返回过程，因此新建一个docker容器只需要几秒钟。

![img](07-docker.assets/1652093324480-073374a2-4ce3-4a56-932b-5a73329736a2.jpeg)

|                | **Docker容器**          | **虚拟机(VM)**              |
| -------------- | ----------------------- | --------------------------- |
| **操作系统**   | 与宿主机共享OS          | 宿主机OS上运行虚拟机OS      |
| **存储大小**   | 镜像小，便于存储和运输  | 镜像庞大（vmdk、vdi等）     |
| **运行性能**   | 几乎无额外性能损失      | 操作系统额外的CPU、内存消耗 |
| **移植性**     | 轻便、灵活，适应于Linux | 笨重，与虚拟化技术耦合度高  |
| **硬件亲和性** | 面向软件开发者          | 面向硬件运维者              |
| **部署速度**   | 快速、秒级              | 较慢，10s以上               |



### 3 Docker常用命令

#### 3.1 帮助启动类命令

- 启动docker：systemctl start docker
- 停止docker：systemctl stop docker
- 重启docker：systemctl restart docker
- 查看docker状态：systemctl status docker
- 开机启动：systemctl enable docker
- 查看docker概要信息：docker info
- 查看docker总体帮助文档：docker --help
- 查看docker命令帮助文档：docker 具体命令 --help



#### 3.2 镜像命令

> docker images

- 列出本地主机上的镜像

- options说明：

  -a：列出本地所有的镜像（含历史映像层）

  -q：只显示镜像ID。

- 例：docker images -a -q (显示所有的镜像ID)

> docker search 某个XXX镜像名字

- 网站：https://hub.docker.com

- 命令：docker search [OPTIONS] 镜像名字

- options说明：

  --limit：只列出N个镜像，默认25个

- 例：docker search --limit 5 redis

  ![image-20221204171601527](07-docker.assets/image-20221204171601527.png)

  | **参数**    | **说明**         |
  | ----------- | ---------------- |
  | NAME        | 镜像名称         |
  | DESCRIPTION | 镜像说明         |
  | STARS       | 点赞数量         |
  | OFFICIAL    | 是否是官方认证的 |
  | AUTOMATED   | 是否是自动构建的 |

> docker pull 某个XXX镜像名字

- 下载镜像

- docker pull 镜像名字[:TAG]

- docker pull 镜像名字

  - 没有TAG就是最新版，等价于docker pull 镜像名字:latest

- 例：docker pull ubuntu

  ![image-20221204172028724](07-docker.assets/image-20221204172028724.png)

> docker system df (查看镜像/容器/数据卷所占的空间)

![image-20221204172244485](07-docker.assets/image-20221204172244485.png)

> docker rmi 某个XXX镜像名字ID

- 删除镜像
- 删除单个：docker rmi -f 镜像ID
- 删除多个：docker rmi -f 镜像名1:TAG 镜像名2:TAG
- 删除全部：docker rmi -f $(docker images -qa)

> 面试题：谈谈docker虚悬镜像是什么？

- 是什么：仓库名、标签都是\<none\>的镜像，俗称虚悬镜像dangling image

  ![image-20221204172631983](07-docker.assets/image-20221204172631983.png)

- 后续Dockerfile章节再介绍



#### 3.3 容器命令

> 前提说明

有镜像才能创建容器，这是根本前提(下载一个CentOS或者ubuntu镜像演示)

![image-20221204172843668](07-docker.assets/image-20221204172843668.png)

- docker pull centos

- docker pull ubuntu

  ![image-20221204172933864](07-docker.assets/image-20221204172933864.png)

- 本次演示用ubuntu演示

> 新建+启动容器

- docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

- OPTIONS说明（常用）：有些是一个减号，有些是两个减号

  - --name="容器新名字"：为容器指定一个名称；

  - -d：后台运行容器并返回容器ID，也即启动守护式容器(后台运行)；

  - -i：以交互模式运行容器，通常与 -t 同时使用；

  - -t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；也即启动交互式容器(前台有伪终端，等待交互)；

  - -P: 随机端口映射，大写P

  - -p: 指定端口映射，小写p

    ![image-20221204173448578](07-docker.assets/image-20221204173448578.png)

- 启动交互式容器(前台命令行)

  docker run -it centos /bin/bash

  ![image-20221204173614661](07-docker.assets/image-20221204173614661.png)

> 列出当前所有正在运行的容器

- docker ps [OPTIONS]
- OPTIONS说明（常用）：
  1. -a：列出当前所有正在运行的容器+历史上运行过的
  2. -l：显示最近创建的容器。
  3. -n：显示最近n个创建的容器。
  4. -q：静默模式，只显示容器编号。

> 退出容器

两种退出方式

1. exit：run进去容器，exit退出，容器停止
2. ctrl+p+q：run进去容器，ctrl+p+q退出，容器不停止

> 启动已停止运行的容器

- docker start 容器ID或者容器名

> 重启容器

- docker restart 容器ID或者容器名

> 停止容器、强制停止容器

- 停止容器：docker stop 容器ID或者容器名
- 强制停止容器：docker kill 容器ID或容器名

> 删除已停止的容器

- docker rm 容器ID
- 一次性删除多个容器实例
  1. docker rm -f $(docker ps -a -q)
  2. docker ps -a -q | xargs docker rm



> 下面是守护式容器(后台服务器管理)
>
> 下载一个Redis6.0.8镜像演示

> 启动守护式容器(后台服务器)

- 在大部分的场景下，我们希望 docker 的服务是在后台运行的，我们可以过 -d 指定容器的后台运行模式。

- docker run -d 容器名

  ```bash
  #使用镜像 centos:latest 以后台模式启动一个容器
  docker run -d centos
  ```

  问题：然后docker ps -a 进行查看，**会发现容器已经退出**。

  很重要的要说明的一点：*Docker容器后台运行，就必须有一个前台进程*。

  容器运行的命令如果不是那些*一直挂起的命令*（比如运行top，tail），就是会自动退出的。

  这个是docker的机制问题，比如你的web容器，我们以nginx为例，正常情况下，我们配置启动服务只需要启动响应的service即可。例如service nginx start

  但是，这样做，nginx为后台进程模式运行，就导致docker前台没有运行的应用，这样的容器后台启动后，会立即自杀因为他觉得他没事可做了。

  所以，最佳的解决方案是，将你要运行的程序以前台进程的形式运行，常见就是命令行模式，表示我还有交互操作，别中断，O(∩_∩)O哈哈~

- redis 前后台启动演示case

  1. 前台交互式启动：docker run -it redis:6.0.8

     ![image-20221204175731219](07-docker.assets/image-20221204175731219.png)

  2. 后台守护式启动：docker run -d redis:6.0.8

     ![image-20221204175511613](07-docker.assets/image-20221204175511613.png)

> 查看容器日志

- docker logs 容器ID

  ![image-20221204180516595](07-docker.assets/image-20221204180516595.png)

> 查看容器内运行的进程

- docker top 容器ID

  ![image-20221204180411705](07-docker.assets/image-20221204180411705.png)

> 查看容器内部细节

- docker inspect 容器ID

> 进入正在运行的容器并以命令行交互

- docker exec -it 容器ID bashShell

  ![image-20221204180748278](07-docker.assets/image-20221204180748278.png)

  ![image-20221204180754358](07-docker.assets/image-20221204180754358.png)

- 重新进入：docker attach 容器ID

- 上述两个区别

  1. attach 直接进入容器启动命令的终端，不会启动新的进程，用exit退出，会导致容器的停止。
  2. exec 是在容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止

- 推荐大家使用 docker exec 命令，因为退出容器终端，不会导致容器的停止。

- 用之前的redis容器实例进入试试

  进入redis服务

  1. docker exec -it 容器ID /bin/bash
  2. docker exec -it 容器ID redis-cli
  3. 一般用-d后台启动的程序，再用exec进入对应容器实例

> 从容器内拷贝文件到主机上

- 容器 → 主机

- docker cp 容器ID:容器内路径 目的主机路径

  ![image-20221204181440561](07-docker.assets/image-20221204181440561.png)

> 导入和导出容器

- export 导出容器的内容留作为一个tar归档文件[对应import命令]

- import 从tar包中的内容创建一个新的文件系统再导入为镜像[对应export]

- 案例

  1. docker export 容器ID > 文件名.tar

     ![image-20221204182153737](07-docker.assets/image-20221204182153737.png)

  2. cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号

     ![image-20221204182605288](07-docker.assets/image-20221204182605288.png)

#### 3.4 总结

![Docker-Command-Diagram.png](07-docker.assets/1652093386636-41aec8f5-bd52-4a99-8c3c-daa61c1d78a5.png)

- attach	Attach to a running container	# 当前 shell 下 attach 连接指定运行镜像
- build    Build an image from a Dockerfile    # 通过 Dockerfile 定制镜像
- commit    Create a new image from a container changes    # 提交当前容器为新的镜像
- cp    Copy files/folders from the containers filesystem to the host path    #从容器中拷贝指定文件或者目录到宿主机中
- create    Create a new container    # 创建一个新的容器，同 run，但不启动容器
- diff    Inspect changes on a container's filesystem    # 查看 docker 容器变化
- events    Get real time events from the server    # 从 docker 服务获取容器实时事件
- exec    Run a command in an existing container    # 在已存在的容器上运行命令
- export    Stream the contents of a container as a tar archive    # 导出容器的内容流作为一个 tar 归档文件[对应 import]
- history    Show the history of an image    # 展示一个镜像形成历史
- images    List images    # 列出系统当前镜像
- import    Create a new filesystem image from the contents of a tarball    # 从tar包中的内容创建一个新的文件系统映像[对应export]
- info    Display system-wide information    # 显示系统相关信息
- inspect    Return low-level information on a container    # 查看容器详细信息
- kill    Kill a running container    # kill 指定 docker 容器
- load    Load an image from a tar archive    # 从一个 tar 包中加载一个镜像[对应 save]
- login    Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器
- logout    Log out from a Docker registry server    # 从当前 Docker registry 退出
- logs    Fetch the logs of a container    # 输出当前容器日志信息
- port    Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口
- pause    Pause all processes within a container    # 暂停容器
- ps    List containers    # 列出容器列表
- pull    Pull an image or a repository from the docker registry server    # 从docker镜像源服务器拉取指定镜像或者库镜像
- push    Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器
- restart    Restart a running container    # 重启运行的容器
- rm    Remove one or more containers    # 移除一个或者多个容器
- rmi    Remove one or more images    # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
- run    Run a command in a new container    # 创建一个新的容器并运行一个命令
- save    Save an image to a tar archive    # 保存一个镜像为一个 tar 包[对应 load]
- search    Search for an image on the Docker Hub    # 在 docker hub 中搜索镜像
- start    Start a stopped containers    # 启动容器
- stop    Stop a running containers    # 停止容器
- tag    Tag an image into a repository    # 给源中镜像打标签
- top    Lookup the running processes of a container    # 查看容器中运行的进程信息
- unpause    Unpause a paused container    # 取消暂停容器
- version    Show the docker version information    # 查看 docker 版本号
- wait    Block until a container stops, then print its exit code    # 截取容器停止时的退出状态值



### 4 Docker镜像

#### 4.1 Docker镜像原理

> 是什么？

*镜像*

是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。

只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。

> 分层的镜像

以我们的pull为例，在下载的过程中我们可以看到docker的镜像好像是在一层一层的在下载

![image-20221205191434256](07-docker.assets/image-20221205191434256.png)

> UnionFS（联合文件系统）

*UnionFS（联合文件系统）*：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

*特性*：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是引导文件系统bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs(root file system)，在bootfs之上。包含的就是典型 Linux 系统中的/dev、/proc、/bin、/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。 

![layer01.jpg](07-docker.assets/1652093445038-27095471-f01f-4978-a28f-d8e2df893dab.jpeg)

**平时我们安装进虚拟机的CentOS都是好几个G，为什么docker这里才200M？？**

![image-20221205191900199](07-docker.assets/image-20221205191900199.png)

对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供 rootfs 就行了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。

> 为什么 Docker 镜像要采用这种分层结构呢

镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用。

比如说有多个镜像都从相同的 base 镜像构建而来，那么 Docker Host 只需在磁盘上保存一份 base 镜像；

同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。



#### 4.2 重点理解

*Docker镜像层都是只读的，容器层是可写的*。

当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”。

所有对容器的改动————无论添加、删除、还是修改文件都只会发生在容器层中。只有容器层是可写的，容器层下面的所有镜像层都是只读的。

![layer04.jpg](07-docker.assets/1652093466220-a1c70673-7ca7-4025-9432-d89152439000.jpeg)



#### 4.3 Docker镜像commit操作案例

- `docker commit`：提交容器副本使之成为一个新的镜像
- `docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]`

> 案例演示ubuntu安装vim

1. 从Hub上下载ubuntu镜像到本地并成功运行

2. 原始的默认Ubuntu镜像是不带着vim命令的

   ![image-20221205202429481](07-docker.assets/image-20221205202429481.png)

3. 外网连通的情况下，安装vim

   docker容器内执行下面两条命令：

   - `apt-get update`：先更新我们的包管理工具
   - `apt-get -y install vim`：然后安装我们需要的vim

   ![image-20221205202719952](07-docker.assets/image-20221205202719952.png)

   ![image-20221205202834762](07-docker.assets/image-20221205202834762.png)

4. 安装完成后，commit我们自己的新镜像

   - `docker commit -m="add vim bash" -a="xk" 80c78608cc60 xk/myubuntu:1.1`

   ![image-20221205203337814](07-docker.assets/image-20221205203337814.png)

5. 启动我们的新镜像并和原来的对比

   ![image-20221205203632831](07-docker.assets/image-20221205203632831.png)



#### 4.4 总结

Docker中的镜像分层，**支持通过扩展现有镜像，创建新的镜像**。类似Java继承于一个Base基础类，自己再按需扩展。

新镜像是从 base 镜像一层一层叠加生成的。每安装一个软件，就在现有镜像的基础上增加一层

![layer03.png](07-docker.assets/1652093459380-335e0f0e-1213-47d9-a17b-f49734699ef2.png)



### 5 本地镜像发布到阿里云

#### 5.1 本地镜像发布到阿里云流程

![image-20221205210411668](07-docker.assets/image-20221205210411668.png)



#### 5.2 将本地镜像推送到阿里云

1. 本地镜像素材原型

   ![image-20221205210517186](07-docker.assets/image-20221205210517186.png)

2. 阿里云开发者平台：https://promotion.aliyun.com/ntms/act/kubernetes.html

3. 选择控制台，进入容器镜像服务

   ![image-20221205210600831](07-docker.assets/image-20221205210600831.png)

4. 选择个人实例

   ![image-20221205210702159](07-docker.assets/image-20221205210702159.png)

5. 命名空间

   ![image-20221205210718299](07-docker.assets/image-20221205210718299.png)

   ![image-20221205210726891](07-docker.assets/image-20221205210726891.png)

6. 仓库名称

   ![image-20221205210830349](07-docker.assets/image-20221205210830349.png)

   ![image-20221205210846926](07-docker.assets/image-20221205210846926.png)

   ![image-20221205210857532](07-docker.assets/image-20221205210857532.png)

7. 进入管理界面获得脚本

   ![image-20221205210913396](07-docker.assets/image-20221205210913396.png)

8. 将镜像推送到阿里云registry

   管理界面脚本

   ![image-20221205211143538](07-docker.assets/image-20221205211143538.png)

   脚本实例

   ```bash
   docker login --username=山海kang registry.cn-hangzhou.aliyuncs.com
   docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/shanhai-xk/myubuntu1.1:[镜像版本号]
   docker push registry.cn-hangzhou.aliyuncs.com/shanhai-xk/myubuntu1.1:[镜像版本号]
   ```

   ![image-20221205211954061](07-docker.assets/image-20221205211954061.png)



#### 5.3 将阿里云上的镜像下载到本地

- `docker pull registry.cn-hangzhou.aliyuncs.com/shanhai-xk/myubuntu1.1:[镜像版本号]`

![image-20221205212437184](07-docker.assets/image-20221205212437184.png)



### 6 本地镜像发布到私有库

#### 6.1 本地镜像发布到私有库流程

![image-20221205214406815](07-docker.assets/image-20221205214406815.png)



#### 6.2 私有库是什么

1. 官方Docker Hub地址：https://hub.docker.com/，中国大陆访问太慢了且准备被阿里云取代的趋势，不太主流。
2. Dockerhub、阿里云这样的公共镜像仓库可能不太方便，涉及机密的公司不可能提供镜像给公网，所以需要创建一个本地私人仓库供给团队使用，基于公司内部项目构建镜像。
3. Docker Registry是官方提供的工具，可以用于构建私有镜像仓库



#### 6.3 将本地镜像推送到私有库

1. 下载镜像Docker Registry

   ![image-20221205214905806](07-docker.assets/image-20221205214905806.png)

2. 运行私有库Registry，相当于本地有个私有Docker hub

   docker run -d -p 5000:5000 -v /shanhai/myregistry/:/tmp/registry --privileged=true registry

   默认情况，仓库被创建在容器的/var/lib/registry目录下，建议自行用容器卷映射，方便于宿主机联调

   ![image-20221205215316799](07-docker.assets/image-20221205215316799.png)

3. 案例演示创建一个新镜像，ubuntu安装ifconfig命令

   - 从Hub上下载ubuntu镜像到本地并成功运行

   - 原始的Ubuntu镜像是不带着ifconfig命令的

     ![image-20221205215841045](07-docker.assets/image-20221205215841045.png)

   - 外网连通的情况下，安装ifconfig命令并测试通过

     `apt-get update`

     `apt-get install net-tools`

     ![image-20221205220114635](07-docker.assets/image-20221205220114635.png)

   - 安装完成后，commit我们自己的新镜像

     命令：在容器外执行，记得

     `docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]`

     `docker commit -m="ifconfig cmd add" -a="zzyy" a69d7c825c4f zzyyubuntu:1.2`

     ![image-20221205220430566](07-docker.assets/image-20221205220430566.png)

   - 启动我们的新镜像并和原来的对比

     官网是默认下载的Ubuntu没有ifconfig命令

     我们自己commit构建的新镜像，新增加了ifconfig功能，可以成功使用。

4. curl验证私服库上有什么镜像

   `curl -XGET http://192.168.88.110:5000/v2/_catalog`

   可以看到，目前私服库没有任何镜像上传过。。。。。。

   ![image-20221205220919775](07-docker.assets/image-20221205220919775.png)

5. 将新镜像xxkkubuntu:1.2修改符合私服规范的Tag

   按照公式：`docker tag 镜像:Tag Host:Port/Repository:Tag`

   **自己host主机IP地址，填写同学你们自己的，不要粘贴错误**，O(∩_∩)O

   使用命令 docker tag 将 xxkkubuntu:1.2 这个镜像修改为 192.168.88.110:5000/xxkkubuntu:1.2

   `docker tag xxkkubuntu:1.2  192.168.88.110:5000/xxkkubuntu:1.2`

   ![image-20221205221310090](07-docker.assets/image-20221205221310090.png)

6. 修改配置文件使之支持http

   ![image-20221205221502714](07-docker.assets/image-20221205221502714.png)

   > 别无脑照着复制，registry-mirrors 配置的是国内阿里提供的镜像加速地址，不用加速的话访问官网的会很慢。
   >
   > *2个配置中间有个逗号','别漏了*，这个配置是json格式的。

   `vim /etc/docker/daemon.json`

   添加：`"insecure-registries": ["192.168.88.110:5000"]`  这一行信息

   ![image-20221205221830677](07-docker.assets/image-20221205221830677.png)

   上述理由：docker默认不允许http方式推送镜像，通过配置选项来取消这个限制。====> *修改完后如果不生效，建议重启docker*。

7. push推送到私服库

   `docker push 192.168.88.110:5000/xxkkubuntu:1.2`

   ![image-20221205222316706](07-docker.assets/image-20221205222316706.png)

8. curl验证私服库上有什么镜像

   `curl -XGET http://192.168.88.110:5000/v2/_catalog`

   ![image-20221205222436142](07-docker.assets/image-20221205222436142.png)

9. pull到本地并运行

   `docker pull 192.168.88.110:5000/xxkkubuntu:1.2`

   ![image-20221205222802483](07-docker.assets/image-20221205222802483.png)

   `docker run -it 镜像ID /bin/bash`

   ![image-20221205222926039](07-docker.assets/image-20221205222926039.png)



### 7 Docker容器数据卷

#### 7.1 前提注意事项

*坑：开启容器时容器卷记得加入*：--privileged=true

> why

Docker挂载主机目录访问**如果出现cannot open directory .: Permission denied**。

解决办法：在挂载目录后多加一个--privileged=true参数即可

如果是CentOS7，安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为，
在SELinux里面挂载目录被禁止掉了额，如果要开启，我们一般使用`--privileged=true`命令，扩大容器的权限解决挂载目录没有权限的问题，也即使用该参数，container内的root拥有真正的root权限，否则，container内的root只是外部的一个普通用户权限。



回忆：

docker run -d -p 5000:5000 `-v /shanhai/myregistry/:/tmp/registry --privileged=true` registry



#### 7.2 容器数据卷简介

> 是什么？

- 一句话：有点类似我们Redis里面的rdb和aof文件

- 将docker容器内的数据保存进宿主机的磁盘中

- 运行一个带有容器卷存储功能的容器实例

  ` docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录      镜像名`

> 能干嘛？

将运用与运行的环境打包镜像，run后形成容器实例运行，但是我们对数据的要求希望是*持久化*的。

Docker容器产生的数据，如果不备份，那么当容器实例删除后，容器内的数据自然也就没有了。

为了能保存数据在docker中我们使用卷。

特点：

1. 数据卷可在容器之间共享或重用数据
2. 卷中的更改可以直接实时生效，爽
3. 数据卷中的更改不会包含在镜像的更新中
4. 数据卷的生命周期一直持续到没有容器使用它为止



#### 7.3 数据卷案例

##### 7.3.1 宿主vs容器之间映射添加容器卷

> 直接命令添加：
>
> `docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名`

`docker run -it --privileged=true --name xk1 -v /tmp/hostdata:/tmp/dockerdata ubuntu`

![image-20221205231418848](07-docker.assets/image-20221205231418848.png)

![image-20221205231420328](07-docker.assets/image-20221205231420328.png)

> 查看数据卷是否挂载成功
>
> `docker inspect 容器ID`

![image-20221205231608131](07-docker.assets/image-20221205231608131.png)

> 容器和宿主机之间数据共享

1. docker修改，主机同步获得
2. 主机修改，docker同步获得
3. docker容器stop，主机修改，docker容器重启后同步主机数据。



##### 7.3.2 读写规则映射添加说明

> 读写(默认)：
>
> `docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名`

默认同上案例，默认就是rw

> 只读
>
> `docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名`

容器实例内部被限制，只能读取不能写

 /容器目录:ro 镜像名  (就能完成功能，此时容器自己只能读取不能写)

此时如果宿主机写入内容，可以同步给容器内，容器可以读取到。

![image-20221205232531612](07-docker.assets/image-20221205232531612.png)



##### 7.3.3 卷的继承和共享

> 容器1完成和宿主机的映射

`docker run -it --privileged=true -v /xkdata/u:/temps --name sh-xk1 ubuntu`

![image-20221205234018374](07-docker.assets/image-20221205234018374.png)

![image-20221205234020601](07-docker.assets/image-20221205234020601.png)

> 容器2继承容器1的卷规则
>
> `docker run -it --privileged=true --volumes-from 父类 --name u2 ubuntu`

`docker run -it --privileged=true --volumes-from sh-xk1 --name sh-xk2 ubuntu`

![image-20221205234358438](07-docker.assets/image-20221205234358438.png)



### 8 Docker常规安装简介

总体步骤：

1. 搜索镜像
2. 拉取镜像
3. 查看镜像
4. 启动镜像---服务端口映射
5. 停止容器
6. 移除容器



#### 8.1 安装tomcat

1. docker hub上面查找tomcat镜像

   `docker search tomcat --limit 5`

   ![image-20221206000138402](07-docker.assets/image-20221206000138402.png)

2. 从docker hub上拉取tomcat镜像到本地

   `docker pull tomcat`

   ![image-20221206000347142](07-docker.assets/image-20221206000347142.png)

3. docker images查看是否有拉取到的tomcat

   `docker images tomcat`

4. 使用tomcat镜像创建容器实例(也叫运行镜像)

   `docker run -d -p 8080:8080 tomcat`

   - -p 小写，主机端口:docker容器端口

   - -P 大写，随机分配端口

     ![image-20221206000524086](07-docker.assets/image-20221206000524086.png)

   - i：交互

   - t：终端

   - d：后台

5. 访问猫首页

   虚拟机中浏览器访问：http://localhost:8080/

   > 问题：

   ![image-20221206001041631](07-docker.assets/image-20221206001041631.png)

   > 解决：

   - 可能没有映射端口或者没有关闭防火墙

   - 把webapps.dist目录换成webapps

     先成功启动tomcat

     ![image-20221206001416325](07-docker.assets/image-20221206001416325.png)

     查看 webapps 文件夹查看为空

     若 webapps 文件夹中为空，则删除 webapps 文件夹，将 webapps.dist 文件夹重命名为 webapps 文件夹名

     之后再次访问猫首页：http://localhost:8080/

     ![image-20221206002053248](07-docker.assets/image-20221206002053248.png)

     ![image-20221206002114778](07-docker.assets/image-20221206002114778.png)

6. 免修改版说明

   `docker pull billygoo/tomcat8-jdk8`

   `docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8`



#### 8.2 安装mysql

##### 8.2.1 mysql5.7 安装

1. docker hub上面查找mysql镜像

   ![image-20221206004807837](07-docker.assets/image-20221206004807837.png)

2. 从docker hub上(阿里云加速器)拉取mysql镜像到本地标签为5.7

   ![image-20221206005113149](07-docker.assets/image-20221206005113149.png)

##### 8.2.2 实例

使用 mysql5.7 镜像创建容器(也叫运行镜像)

命令出处，哪里来的？

`docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag`

![](07-docker.assets/image-20221206005236357.png)

> 简单版：

1. 使用mysql镜像

   `docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7`

   `docker ps`

   `docker exec -it 容器ID /bin/bash`

   `mysql -uroot -p`

   ![image-20221206005921328](07-docker.assets/image-20221206005921328.png)

2. 建库建表插入数据

   ![image-20221206010248867](07-docker.assets/image-20221206010248867.png)

3. 外部Win10也来连接运行在dokcer上的mysql容器实例服务

   ![image-20221206010447453](07-docker.assets/image-20221206010447453.png)

4. 问题

   - 插入中文数据试试

     ![image-20221206010730872](07-docker.assets/image-20221206010730872.png)

     为什么报错？

     docker上默认字符集编码隐患

     docker里面的mysql容器实例查看，内容如下：`SHOW VARIABLES LIKE 'character%'`。

     ![image-20221206010853753](07-docker.assets/image-20221206010853753.png)

   - 删除容器后，里面的mysql数据如何办，会永远丢失。

> 实战版

1. 新建mysql容器实例

   ```
   docker run -d -p 3306:3306 --privileged=true -v /zzyyuse/mysql/log:/var/log/mysql -v /zzyyuse/mysql/data:/var/lib/mysql -v /zzyyuse/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456  --name mysql mysql:5.7
   
   
   docker run -d -p 3306:3306 --privileged=true 
   -v /zzyyuse/mysql/log:/var/log/mysql 
   -v /zzyyuse/mysql/data:/var/lib/mysql 
   -v /zzyyuse/mysql/conf:/etc/mysql/conf.d 
   -e MYSQL_ROOT_PASSWORD=123456  
   --name mysql mysql:5.7
   ```

2. 在宿主机的 /zzyyuse/mysql/conf 目录下新建 my.cnf

   通过容器卷同步给mysql容器实例

   my.cnf 里面输入：

   ```properties
   [client]
   default_character_set=utf8
   [mysqld]
   collation_server = utf8_general_ci
   character_set_server = utf8
   ```

   ![image-20221206011638205](07-docker.assets/image-20221206011638205.png)

3. 重新启动mysql容器实例再重新进入并查看字符编码

   ![image-20221206012038111](07-docker.assets/image-20221206012038111.png)

4. 再新建库新建表再插入中文测试

   ![image-20221206012433925](07-docker.assets/image-20221206012433925.png)

   ![image-20221206012632795](07-docker.assets/image-20221206012632795.png)

> 总结

之前的DB：无效

修改字符集操作+重启mysql容器实例

之后的DB：有效，需要新建

结论：*docker安装完MySQL并run出容器后，建议请先修改完字符集编码后再新建mysql库-表-插数据*。

> 假如将当前容器实例删除，再重新来一次，之前建的db01实例还有吗？

有的。



#### 8.3 安装redis

1. 从docker hub上(阿里云加速器)拉取redis镜像到本地标签为6.0.8

   `docker pull redis:6.0.8`

   ![image-20221206014455409](07-docker.assets/image-20221206014455409.png)

2. 入门命令

   `docker run -d -p 6379:6379 redis:6.0.8`

   `docker run -d -p 6379:6379 redis:6.0.8`

   ![image-20221206014834780](07-docker.assets/image-20221206014834780.png)

3. 命令提醒：容器卷记得加入`--privileged=true`

   Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied

   解决办法：在挂载目录后多加一个`--privileged=true`参数即可

4. 在CentOS宿主机下新建目录/app/redis

   `mkdir -p /app/redis`

   ![image-20221206015111382](07-docker.assets/image-20221206015111382.png)

5. 将一个redis.conf文件模板拷贝进/app/redis目录下

   /app/redis目录下修改redis.conf文件

   - 开启redis验证 [可选]

     requirepass 123

   - 允许redis外地连接，必须注释掉 # bind 127.0.0.1

     ![image-20221206015706799](07-docker.assets/image-20221206015706799.png)

   - daemonize no

     将 `daemonize yes` 注释起来或者 `daemonize no` 设置，因为该配置和`docker run中-d`参数冲突，会导致容器一直启动失败。

     ![image-20221206015831411](07-docker.assets/image-20221206015831411.png)

   - 开启redis数据持久化：`appendonly yes` [可选]

6. 使用redis6.0.8镜像创建容器(也叫运行镜像)

   ```
   docker run  -p 6379:6379 --name myr3 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis:6.0.8 redis-server /etc/redis/redis.conf
   
   
   docker run -p 6379:6379 --name myr3 --privileged=true 
   -v /app/redis/redis.conf:/etc/redis/redis.conf 
   -v /app/redis/data:/data 
   -d redis:6.0.8 redis-server /etc/redis/redis.conf
   ```

   ![image-20221206020401801](07-docker.assets/image-20221206020401801.png)

7. 测试redis-cli连接上来

   `docker exec -it 运行着Rediis服务的容器ID redis-cli`

   ![image-20221206020551224](07-docker.assets/image-20221206020551224.png)

8. 证明docker启动使用了我们自己指定的配置文件

   - 修改配置文件前

     ![image-20221206020711952](07-docker.assets/image-20221206020711952.png)

   - 修改配置文件后

     记得重启服务：`docker restart 容器名或容器ID`

     ![image-20221206020905188](07-docker.assets/image-20221206020905188.png)

     ![image-20221206021145768](07-docker.assets/image-20221206021145768.png)

+++

## 二、高级篇

### 1 Docker复杂安装详说

#### 1.1 安装mysql主从复制







































































































































































































































