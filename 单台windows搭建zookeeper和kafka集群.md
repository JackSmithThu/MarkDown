---
title: 单台windows搭建zookeeper和kafka集群
date: 2017-05-04 18:11:39
tags: [Kafka,Zookeeper,Windows]
categories: 技术
---

我们的目标是在一台机器上部署三个zookeeper的server，并在此基础上部署三个zookeeper的server。

# 1.zookeeper的部署

zookeeper的部署采用伪集群模式，即在一台电脑上部署多个server。

## 1.1.建立文件夹，存放项目

在你喜欢的位置（我的目录为D:\WorkSpace）建立一个名为zookeeper的文件夹，并在该文件夹下建立三个目录，分别名为server1，server2和server3。完成之后如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040011.png)

## 1.2.下载zookeeper

下载地址：http://apache.fayea.com/zookeeper/。或者直接去zookeeper官网自己找对应的版本也是一样的。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040012.png)

## 1.3.解压文件并创建一些目录

将下载好的压缩包解压到server1目录下，并在server1目录下建三个文件夹，分别名为data，dataLog和logs。完成之后应该是这样的。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040013.png)

注意：压缩包解压的时候直接选【解压到当前文件夹】即可。所以上图中的zookeeper-3.4.10文件夹打开之后应该是这样的：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040014.png)

## 1.4.在server1/data目录下，创建一个叫做myid的文件。

注意，这个文件不需要任何扩展名，所以显示的形式如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040015.png)

如果你看到的myid文件不是这样的，说明你的扩展名没有去除。在windows系统下，文件扩展名默认是被隐藏的。在【查看】选项卡里勾选【文件扩展名】，你就能看到被隐藏的扩展名了。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040016.png)

## 1.5.打开myid文件，在里面添加对应的id

因为我们目前配置的是server1，所以添加id为1，然后关闭即可。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040017.png)

这里打开myid文件使用的编辑器是notepad++，如果没有安装的话，可以下载一个安装一下。下载地址：https://notepad-plus-plus.org/。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040018.png)

## 1.6.修改配置文件
在server1/zookeeper-3.4.10/conf目录下，新建zoo.cfg文件，加入内容如下：

```
tickTime=2000
initLimit=5
syncLimit=2
dataDir=D:/WorkSpace/zookeeper/server1/data
dataLogDir=D:/WorkSpace/zookeeper/server1/dataLog
clientPort=2181
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890
```

注意，dataDir和dataLogDir中的D:/WorkSpace目录需要改成你自己的目录。

这里对zoo.cfg中的内容进行简要地说明：

> tickTime为客户端与服务器之间或服务器互相之间的心跳间隔，以ms为单位。
> initLimit为leader与follower进行初始连接时最多能够容忍的心跳数。
> syncLimit为follower与leader请求和应答之间最多能容忍的心跳数。
> dataDir为数据文件目录。
> dataLogDir为日志文件目录。
> clientPort为客户端连接服务器的端口，zookeeper监听此接口接收客户端访问请求。
> 最后三行为集群中每个服务器的ip和端口号。前一个端口为leader和follower通信端口，后一个端口为选举新leader时所用的端口。一般来说，在多台电脑的情况下，每台服务器上的端口配置是一样的。不过我们现在使用同一台电脑配置三个server，为了避免冲突，只能把它们改成不一样。

## 1.7.复制server1中的内容到server2和server3中，并修改相应配置

将server1中的内容分别复制到server2和server3中，并修改/data/myid文件和/zookeeper-3.4.10/conf/zoo.cfg文件。

myid文件只需要将其中的内容改成服务器的序号即可。

zoo.cfg文件需要修改dataDir，dataLogDir和clientPort。修改后的三个文件如下：

server1：

> tickTime=2000
> initLimit=5
> syncLimit=2
> dataDir=D:/WorkSpace/zookeeper/server1/data
> dataLogDir=D:/WorkSpace/zookeeper/server1/dataLog
> clientPort=2181
> server.1=127.0.0.1:2888:3888
> server.2=127.0.0.1:2889:3889
> server.3=127.0.0.1:2890:3890

server2：

> tickTime=2000
> initLimit=5
> syncLimit=2
> dataDir=D:/WorkSpace/zookeeper/server2/data
> dataLogDir=D:/WorkSpace/zookeeper/server2/dataLog
> clientPort=2182
> server.1=127.0.0.1:2888:3888
> server.2=127.0.0.1:2889:3889
> server.3=127.0.0.1:2890:3890

server3：

> tickTime=2000
> initLimit=5
> syncLimit=2
> dataDir=D:/WorkSpace/zookeeper/server3/data
> dataLogDir=D:/WorkSpace/zookeeper/server3/dataLog
> clientPort=2183
> server.1=127.0.0.1:2888:3888
> server.2=127.0.0.1:2889:3889
> server.3=127.0.0.1:2890:3890

## 1.8.启动服务器

分别进入三个server的/zookeeper-3.4.10/bin目录下，运行：zkServer start 。结果如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040019.png)

第一个server启动的时候会报错，这是由于它想要和其他两个server进行通信，而另外两个server还没有搭建起来。不用理会这个错误，继续打开新的cmd窗口，启动其他server就好。注意，原来的窗口不要关！所有server都启动了之后，显示如下：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040020.png)

如果报Invalid arguments, exiting abnormally 的异常的话，只需要在 zkServer start 命令中去掉start，即输入zkServer即可。

## 1.9.连接客户端

进入任意一个zookeeper-3.4.10目录下，用以下命令启动客户端：zkCli –server 127.0.0.1:2181  
，显示如下结果：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040021.png)

输入help，显示可以执行的指令。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040022.png)

做到这一步，zookeeper集群的搭建就完成了。接下来我们要在此基础上搭建kafka的集群。

# 2.kafka的部署

kafka在设计的时候就依赖于zookeeper，对于kafka而言，zookeeper相当于它的文件系统。每个节点的信息都保存在zookeeper对应的路径下。

**（1）Broker注册**：Broker在zookeeper中保存为一个临时节点，节点的路径是/brokers/ids/[brokerid],每个节点会保存对应broker的IP以及端口等信息。

**（2）Topic注册**：在kafka中,一个topic会被分成多个区并被分到多个broker上，分区的信息以及broker的分布情况都保存在zookeeper中，根节点路径为/brokers/topics,每个topic都会在topics下建立独立的子节点，每个topic节点下都会包含分区以及broker的对应信息。

**（3）消费者与分区的对应关系**：对于每个消费者分组，kafka都会为其分配一个全局唯一的Group ID,分组内的所有消费者会共享该ID,kafka还会为每个消费者分配一个consumer ID,通常采用hostname:uuid的形式。在kafka的设计中规定，对于topic的每个分区，最多只能被一个消费者进行消费，也就是消费者与分区的关系是一对多的关系。消费者与分区的关系被存储在zookeeper中节点的路径为 /consumers/[group_id]/owners/[topic]/[broker_id-partition_id],该节点的内容就是消费者的Consumer ID。

**（4）消费者负载均衡**：消费者服务启动时，会创建一个属于消费者节点的临时节点，节点的路径为 /consumers/[group_id]/ids/[consumer_id],该节点的内容是该消费者订阅的Topic信息。每个消费者会对/consumers/[group_id]/ids节点注册Watcher监听器，一旦消费者的数量增加或减少就会触发消费者的负载均衡。消费者还会对/brokers/ids/[brokerid]节点进行监听，如果发现服务器的Broker服务器列表发生变化，也会进行消费者的负载均衡。

**（5）消费者的offset**：在kafka的消费者API分为两种(1)High Level API：由zookeeper维护消费者的offset (2) Low Level API：自己的代码实现对offset的维护。由于自己维护offset往往比较复杂，所以多数情况下都是使用High Level的API offset在zookeeper中的节点路径为/consumers/[group_id]/offsets/[topic]/[broker_id-part_id],该节点的值就是对应的offset。

看到这里，你就会明白kafka和zookeeper之间的关系了。我们现在就是要在已经配置好的zookeeper集群上配置kafka集群。

## 2.1.建立对应目录

还是找到你喜欢的那个目录（我的是D:\WorkSpace）,建立一个叫做kafka的文件夹，里面建立三个子目录，分别叫做：kafka1，kafka2和kafka3。完成之后应该是这个样子：
![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040023.png)

## 2.2.下载kafka并解压到每个文件夹

kafka下载地址：
https://www.apache.org/dyn/closer.cgi?path=/kafka/0.10.2.1/kafka_2.12-0.10.2.1.tgz
下载之后是一个压缩包，还是直接解压到kafka1，kafka2和kafka3这三个文件夹就行。解压完后，在 kafka1，kafka2和kafka3目录下分别添加一个kafkaLog目录。最后的结果应该是这个样子的：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040024.png)

## 2.3.修改配置文件：

分别打开kafka1，kafka2和kafka3三个目录中的kafka_2.12-0.10.2.1/config路径下的consumer.properties文件，将里面的内容改成：

```
zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183
group.id=logGroup
```

再打开这三个目录下的server.properties文件，按照下面内容进行修改：

kafka1：

```
broker.id=0
log.dirs =D:/WorkSpace/kafka/kafka1/kafkaLog
listeners=PLAINTEXT://127.0.0.1:9092
port=9092
zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880
```

kafka2：

```
broker.id=1
log.dirs =D:/WorkSpace/kafka/kafka2/kafkaLog
listeners=PLAINTEXT://127.0.0.1:9093
port=9093
zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880
```

kafka3：

```
broker.id=2
log.dirs =D:/WorkSpace/kafka/kafka3/kafkaLog
listeners=PLAINTEXT://127.0.0.1:9094
port=9094
zookeeper.connect=127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880
```

## 2.4.启动kafka服务器

进入D:/WorkSpace/kafka/kafka1/目录下，输入
.\bin\windows\kafka-server-start.bat .\config\server.properties  命令，注意不要漏掉命令中的“.”。得到的结果如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040025.png)

同样地，进入另外两个kafka2 和 kafka3目录下，把另外两个kafka服务器也启动起来。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040026.png)

如果你最后得到的结果是这样的，那么恭喜你，zookeeper和kafka集群已经搭建完成了。

# 参考链接：
（1）在单机上实现ZooKeeper伪机群/伪集群部署 ：

http://blog.csdn.net/poechant/article/details/6633923

（2）一台机器上安装zookeeper和kafka集群：

http://blog.csdn.net/u013244038/article/details/53938997

## 操作系统版本：windows 10

## zookeeper版本：3.4.10

## kafka版本：2.12-0.10.2.1
