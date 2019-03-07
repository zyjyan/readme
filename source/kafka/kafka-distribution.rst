KAFKA分布式安装及使用
~~~~~~~~~~~~~~~~~~~~~

本文中，将选择ubuntu16.04(AMD64)操作系统作为例，进行KAFKA分布式部署安装及实践。
kafka依赖于zookeeper和java，因此在安装kafka之前，请确保zookeeper及java正常运行。具体java及zookeeper安装，可见hadoop ha分布式部署相关章节。我们本文中，安装
kafka使用hadoop用户安装。



1-软件及环境准备
----------------
1.1 本次使用三台服务器安装kafka。其中，master、master-0、slaver-1安装kafka集群，slaver-1、slaver-2、slaver-3安装zookeeper集群。

.. code-block:: console

 package                    hostname
 kafka、java                 master
 kafka、java                 master-0
 kafka、java、zookeeper      slaver-1
 java、zookeeper      slaver-2
 java、zookeeper      slaver-3

 # kafka 版本2.12 zookeeper版本3.4.12 java 1.8

.. end



1.2 下载软件包。`KAFKA源选取合适的包下载安装 <https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.0/kafka_2.11-2.1.0.tgz>`_.
将kafka安装包在master节点解压到/opt目录，并改名为kafka。


.. code-block:: console

  $ tar -xzvf kafka_2.12-1.1.0.tgz -C /opt/
  $ cd /opt
  $ mv kafka_2.12-1.1.0/ kafka

.. end

1.3 修改kafka配置文件;

.. code-block:: console

 root@master:/opt/kafka/config# vi server.properties
 root@master:/opt/kafka/config# grep -vE  '^#|^$' server.properties

 broker.id=1 #根据服务器配置，每个服务器号唯一
 num.network.threads=3
 num.io.threads=8
 socket.send.buffer.bytes=102400
 socket.receive.buffer.bytes=102400
 socket.request.max.bytes=104857600
 log.dirs=/var/kafka-logs
 num.partitions=1
 num.recovery.threads.per.data.dir=1
 offsets.topic.replication.factor=1
 transaction.state.log.replication.factor=1
 transaction.state.log.min.isr=1
 log.retention.hours=168
 log.segment.bytes=1073741824
 log.retention.check.interval.ms=300000
 zookeeper.connect=slaver-1:2181,slaver-2:2181,slaver-3:2181 #zookeeper 集群节点；
 zookeeper.connection.timeout.ms=6000 
 group.initial.rebalance.delay.ms=0

.. end

1.4 将kafka解压包使用scp命令拷贝至集群其他节点，并将文件mv至/opt目录下，修改配置文件中的broker.id项。

1.5 修改各个服务器文件目录权限。

.. code-block:: console

 root@master:/opt# chown -R hadoop-1:hadoop-1 kafka/

.. end


2-启动服务
----------

2.1 启动zookeeper集群，并确保zookeeper正常运行。
2.2 登录到三台服务器上，使用hadoop用户启动kafka。

.. code-block:: console

 hadoop@master:/opt/kafka$ bin/kafka-server-start.sh config/server.properties & # 此为临时启动命令，关闭窗口后kafka停止运行，后台日志报错
 # 后端运行命令。 # ./kafka-server-start.sh ../config/server.properties 1>/dev/null 2>&1 &
 # 1>/dev/null  2>&1 是将命令产生的输入和错误都输入到空设备，也就是不输出的意思。

 hadoop@master:/opt/kafka$ jps
 18097 NameNode
 22690 Kafka
 18538 ResourceManager
 23246 Jps
 18462 DFSZKFailoverController
 
.. end




3-创建TOPIC
-----------

创建一个名称为test-zhao的Topic，3个分区，并且复制因子为1，执行如下命令:



.. code-block:: console

  hadoop-1@master:/opt/kafka$ bin/kafka-topics.sh --create --zookeeper slaver-1:2181,slaver-2:2181,slaver-3:2181 --replication-factor 1 --partitions 3 --topic test-zhao

.. end

创建成功后，可在kafka数据目录查看，分别在master、master-0、slaver-1主机的kafka数据目录，
即配置文件中的log.dirs=/var/kafka-logs，形成partition为 test-zhao-0,test-zhao-1,test-zhao-2的文件夹，
文件夹下xxx.log是消息集文件， xxx.index 偏移量索引文件 ，xxx.timeindex 时间戳索引文件；

查看已创建的topic；

.. code-block:: console

 hadoop-1@master:/opt/kafka$ ./bin/kafka-topics.sh --list --zookeeper slaver-2:2181
 test-zhao

.. end

# 查看topic信息。

.. code-block:: console

 hadoop-1@master:/var/log/kafka-logs/test-zhao-0$  /opt/kafka//bin/kafka-topics.sh --describe --zookeeper slaver-1:2181 --topic test-zhao
 Topic:test-zhao	PartitionCount:3	ReplicationFactor:1	Configs:
	Topic: test-zhao	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: test-zhao	Partition: 1	Leader: 2	Replicas: 2	Isr: 2
	Topic: test-zhao	Partition: 2	Leader: 3	Replicas: 3	Isr: 3

.. end

可以看到 partition0在id为1的broker上，其数据副本也在broker1上，并且broker1为leader状态。
我们可以通过Kafka自带的bin/kafka-console-producer.sh和bin/kafka-console-consumer.sh脚本，来验证演示如果发布消息、消费消息。

4-模拟客户端发送消息
--------------------

Kafka自带一个命令行客户机，它将从文件或标准输入中获取输入，并将其作为消息发送到Kafka集群。默认情况下，每一行都将作为单独的消息发送。
使用如下指令发送消息。

.. code-block:: console

 hadoop-1@master:/opt/kafka$ bin/kafka-console-producer.sh --broker-list master:9092, master-0:9092, slaver-1:9092 --topic test-zhao
 >cecgw-kafka-zhaoyuanjie-first

.. end

我们在master节点，模拟发送了"cecgw-kafka-zhaouanjie-first"的消息。我们通过字符串查找，可以看到，该消息落到了slaver-1节点日志中。

.. code-block:: console

 hadoop-1@slaver-1:/var/log/kafka-logs/test-zhao-2$ grep 'cecgw'  ./ -R
 Binary file ./00000000000000000000.log matches
 可以看出该文件中，有发送的消息内容。通过kafka自带的命令，可以将二进制文件，转化为字符类型文件。

 opt/kafka/bin/kafka-run-class.sh kafka.tools.DumpLogSegments --files /var/log/kafka-logs/test-zhao-2/00000000000000000000.log --print-data-log
 Dumping /var/log/kafka-logs/test-zhao-2/00000000000000000000.log
 Starting offset: 0
 offset: 0 
 position: 0 
 CreateTime: 1551680240403 
 isvalid: true 
 keysize: -1 
 valuesize: 29 
 magic: 2  #这个占用1个字节，主要用于标识 Kafka 版本。
 compresscodec: NONE 
 producerId: -1 
 producerEpoch: -1 
 sequence: -1 
 isTransactional: false 
 headerKeys: [] 
 payload: cecgw-kafka-zhaoyuanjie-first

 # 查看index文件内容
 hadoop-1@slaver-1:/var/log/kafka-logs/test-zhao-2$ /opt/kafka/bin/kafka-run-class.sh kafka.tools.DumpLogSegments --files /var/log/kafka-logs/test-zhao-2/00000000000000000000.index --print-data-log
 Dumping /var/log/kafka-logs/test-zhao-2/00000000000000000000.index
 offset: 0 position: 0

.. end

我们仅在slaver-1节点查找到了该字符串，因此数据备份因子为1生效。



5-启动消费者
------------
同样，KAFKA可以使用命令行启动消费者服务。再启动一个shell终端，执行如下命令：

.. code-block:: console

 hadoop-1@slaver-1:/var/log/kafka-logs/test-zhao-2$ /opt/kafka/bin/kafka-console-consumer.sh --zookeeper slaver-1:2181, slaver-2:2181, slaver-3:2181 --from-beginning --topic test-zhao
 Using the ConsoleConsumer with old consumer is deprecated and will be removed in a future major release. Consider using the new consumer by passing [bootstrap-server] instead of [zookeeper].

 cecgw-kafka-zhaoyuanjie-first

.. end

目前已经正常收到消息。

查看消费者组。

.. code-block:: console

 hadoop-1@master:/home/ubuntu$ /opt/kafka/bin/kafka-consumer-groups.sh --zookeeper slaver-1:2181 --list
 Note: This will only show information about consumers that use ZooKeeper (not those using the Java consumer API).
 console-consumer-40107
 console-consumer-99536
 console-consumer-60818
 console-consumer-57442
 testgroup
 console-consumer-61493
 console-consumer-79230
 console-consumer-10997
 console-consumer-20932
 console-consumer-1732
 console-consumer-86987

.. end

查看某一消费者组描述信息。

.. code-block:: console

 hadoop-1@master:/home/ubuntu$ /opt/kafka/bin/kafka-consumer-groups.sh --zookeeper slaver-1:2181 --group console-consumer-40107 --describe
 Note: This will only show information about consumers that use ZooKeeper (not those using the Java consumer API).

 TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     
 test-new        0          11              39996           39985           -               
 test-new        1          11              40002           39991           -               
 test-new        2          12              33335           33323           -  

 #消费的topic名称、partition id、consumer group最后一次提交的offset、最后提交的生产消息offset、消费offset与生产offset之间的差值、当前消费topic-partition的group成员id.

.. end

6. 删除topic。

.. code-block:: console

 hadoop-1@master:/opt/kafka$ bin/kafka-topics.sh  --delete --zookeeper slaver-1:2181  --topic test-zhao
 Topic test-zhao is marked for deletion.
 Note: This will have no impact if delete.topic.enable is not set to true.
 [2019-03-04 15:05:49,125] INFO [GroupMetadataManager brokerId=1] Removed 0 expired offsets in 1 milliseconds. (kafka.coordinator.group.GroupMetadataManager)
 [2019-03-04 15:05:49,172] INFO [ReplicaFetcherManager on broker 1] Removed fetcher for partitions  (kafka.server.ReplicaFetcherManager)
 [2019-03-04 15:05:49,172] INFO [ReplicaAlterLogDirsManager on broker 1] Removed fetcher for partitions  (kafka.server.ReplicaAlterLogDirsManager)
 [2019-03-04 15:05:49,177] INFO [ReplicaFetcherManager on broker 1] Removed fetcher for partitions test-zhao-0 (kafka.server.ReplicaFetcherManager)
 [2019-03-04 15:05:49,177] INFO [ReplicaAlterLogDirsManager on broker 1] Removed fetcher for partitions test-zhao-0 (kafka.server.ReplicaAlterLogDirsManager)
 [2019-03-04 15:05:49,180] INFO [ReplicaFetcherManager on broker 1] Removed fetcher for partitions  (kafka.server.ReplicaFetcherManager)
 [2019-03-04 15:05:49,180] INFO [ReplicaAlterLogDirsManager on broker 1] Removed fetcher for partitions  (kafka.server.ReplicaAlterLogDirsManager)
 [2019-03-04 15:05:49,181] INFO [ReplicaFetcherManager on broker 1] Removed fetcher for partitions test-zhao-0 (kafka.server.ReplicaFetcherManager)
 [2019-03-04 15:05:49,181] INFO [ReplicaAlterLogDirsManager on broker 1] Removed fetcher for partitions test-zhao-0 (kafka.server.ReplicaAlterLogDirsManager)
 [2019-03-04 15:05:49,219] INFO Log for partition test-zhao-0 is renamed to /var/log/kafka-logs/test-zhao-0.fd5fa204b9a54209afd39ced6263e026-delete and is scheduled for deletion (kafka.log.LogManager)

.. end

可以看到各个节点上的partition均已经删除掉。



7、创建一个复制因子为2,partition为3的主题：

.. code-block:: console

  > bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 3 --topic my-replicated-topic

.. end




   
