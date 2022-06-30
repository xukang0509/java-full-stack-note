# Zookeeper

## 零、课程介绍

### 1 本课程的重点内容：

1. Zookeeper分布式锁案例
2. Paxos算法
3. ZAB协议
4. CAP
5. 源码：
   1. zk服务端初始化源码
   2. 服务器端加载数据源码
   3. 选举算法
   4. 状态同步算法
   5. Leader启动源码
   6. Follower启动源码
   7. 客户端启动源码



### 2 课程特色

1. 新	Zookeeper3.5.7
2. 细    注释详细，文档中代码复制粘贴就可以
3. 全    几乎涵盖了所有关于ZK相关讲解
4. 生动PPT动画



### 3 技术基础要求

JavaSE、Maven、IDEA、Linux常用命令

------

## 一、Zookeeper 入门

### 1 概述

Zookeeper 是一个开源的分布式的，为分布式框架提供协调服务的 Apache 项目。

![image-20220507191544599](03-Zookeeper.assets/image-20220507191544599.png)



![image-20220507202524453](03-Zookeeper.assets/image-20220507202524453.png)



### 2 特点

![image-20220507202935025](03-Zookeeper.assets/image-20220507202935025.png)

1. Zookeeper：一个领导者（Leader），多个跟随者（Follower）组成的集群。
2. 集群中只要有**半数以上**节点存活，Zookeeper集群就能正常服务。所以Zookeeper适合安装奇数台服务器。
3. 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
4. 更新请求顺序执行，来自同一个Client的更新请求按其发送顺序依次执行（FIFO）。
5. 数据更新原子性，一次数据更新要么成功，要么失败。
6. 实时性，在一定时间范围内，Client 能读到最新数据。



### 3 数据结构

ZooKeeper 数据模型的结构与 Unix 文件系统很类似，整体上可以看作是一棵树，每个
节点称做一个 ZNode。

每一个 ZNode 默认能够存储 1MB 的数据，每个 ZNode 都可以通过
其路径唯一标识。

![image-20220507203042250](03-Zookeeper.assets/image-20220507203042250.png)



### 4 应用场景

提供的服务包括：

- 统一命名服务
- 统一配置管理
- 统一集群管理
- 服务器节点动态上下
  线
- 软负载均衡等。



![image-20220507203326932](03-Zookeeper.assets/image-20220507203326932.png)

![image-20220507203541911](03-Zookeeper.assets/image-20220507203541911.png)

![image-20220507203712764](03-Zookeeper.assets/image-20220507203712764.png)

![image-20220507203801757](03-Zookeeper.assets/image-20220507203801757.png)

![image-20220507203909982](03-Zookeeper.assets/image-20220507203909982.png)



### 5 下载地址

官网地址：https://zookeeper.apache.org/

![image-20220507204121541](03-Zookeeper.assets/image-20220507204121541.png)



![image-20220507204233535](03-Zookeeper.assets/image-20220507204233535.png)



![image-20220507204335587](03-Zookeeper.assets/image-20220507204335587.png)



下载 Linux 环境安装的 tar 包

![image-20220507204440858](03-Zookeeper.assets/image-20220507204440858.png)



### 6 本地安装模式

#### 6.1 本地模式安装

##### 6.1.1 安装前准备

1. 安装JDK：`yum install -y java-1.8.0-openjdk.x86_64`

2. 拷贝 apache-zookeeper-3.5.7-bin.tar.gz 安装包到 Linux 系统下的目录`/opt/model`

3. 解压到指定目录

   `tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module/`

4. 修改名称

   `mv apache-zookeeper-3.5.7-bin/ 
   zookeeper-3.5.7`



##### 6.1.2 配置修改

1. 将`/opt/module/zookeeper-3.5.7/conf `这个路径下的 `zoo_sample.cfg` 修改为` zoo.cfg`：

   `mv zoo_sample.cfg zoo.cfg`

2. 打开 zoo.cfg 文件，修改 dataDir 路径：

   `vi zoo.cfg`

   修改如下内容：

   `dataDir=/opt/module/zookeeper-3.5.7/zkData`

3. 在`/opt/module/zookeeper-3.5.7/`这个目录上创建 zkData 文件夹

   `mkdir zkData`



##### 6.1.3 操作ZooKeeper

1. 启动 Zookeeper：`bin/zkServer.sh start`

2. 查看进程是否启动

3. 查看状态：`bin/zkServer.sh status`

   ```
   [root@localhost zookeeper-3.5.7]# bin/zkServer.sh status
   /usr/bin/java
   ZooKeeper JMX enabled by default
   Using config: /opt/model/zookeeper-3.5.7/bin/../conf/zoo.cfg
   Client port found: 2181. Client address: localhost.
   Mode: standalone
   ```

4. 启动客户端：`bin/zkCli.sh`
5. 退出客户端：`
   [zk: localhost:2181(CONNECTED) 0] quit`
6. 停止 Zookeeper：`
   bin/zkServer.sh stop`



#### 6.2 配置参数解读

```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/opt/model/zookeeper-3.5.7/zkData
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
```

Zookeeper中的配置文件zoo.cfg中参数含义解读如下：

1. `tickTime = 2000`：通信心跳时间，Zookeeper服务器与客户端心跳时间，单位毫秒

![image-20220508165543540](03-Zookeeper.assets/image-20220508165543540.png)



2. `initLimit = 10`：LF初始通信时限

![image-20220508165624296](03-Zookeeper.assets/image-20220508165624296.png)



3. `syncLimit = 5`：LF同步通信时限

![image-20220508165701110](03-Zookeeper.assets/image-20220508165701110.png)



4. dataDir：保存Zookeeper中的数据

   注意：默认的tmp目录，容易被Linux系统定期删除，所以一般不用默认的tmp目录。



5. `clientPort = 2181`：客户端连接端口，通常不做修改。











































































































































