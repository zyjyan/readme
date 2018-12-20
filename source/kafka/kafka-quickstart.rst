KAFKA安装及使用
~~~~~~~~~~~~~~~

前面简单介绍了KAFKA的基本概念及用途。本文中，将选择ubuntu14.04(AMD64)操作系统作为例，进行KAFKA安装实践。

1-软件准备
--------
下载软件包。`KAFKA源选取合适的包下载安装 <https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.0/kafka_2.11-2.1.0.tgz>`_.

.. code-block:: console

  $ tar -xzvf kafka_2.12-1.1.0.tgz -C /opt/
  $ cd /opt
  $ mv kafka_2.12-1.1.0/ kafka

.. end

将下载的软件包解压缩到/opt目录。

2-运行KAFKA服务端
------------------
Kafka使用ZooKeeper，所以如果你还没有ZooKeeper服务器，你需要先启动它。KAFKA中集成了一个ZooKeeper实例。

.. code-block:: console

  root@ubuntu:/opt/kafka# ./bin/zookeeper-server-start.sh config/zookeeper.properties
  [2018-12-19 14:44:06,230] INFO Server environment:user.home=/root (org.apache.zookeeper.server.ZooKeeperServer)
  [2018-12-19 14:44:06,230] INFO Server environment:user.dir=/opt/kafka (org.apache.zookeeper.server.ZooKeeperServer)
  [2018-12-19 14:44:06,243] INFO tickTime set to 3000 (org.apache.zookeeper.server.ZooKeeperServer)


  [2018-12-19 14:44:06,243] INFO minSessionTimeout set to -1 (org.apache.zookeeper.server.ZooKeeperServer)
  [2018-12-19 14:44:06,243] INFO maxSessionTimeout set to -1 (org.apache.zookeeper.server.ZooKeeperServer)
  [2018-12-19 14:44:06,254] INFO binding to port 0.0.0.0/0.0.0.0:2181 (org.apache.zookeeper.server.NIOServerCnxnFactory)

.. end

在另一个shell终端启动KAFKA服务端。

.. code-block:: console

  root@ubuntu:/opt/kafka# bin/kafka-server-start.sh config/server.properties
  [2018-12-19 14:46:41,891] INFO Registered kafka:type=kafka.Log4jController MBean (kafka.utils.Log4jControllerRegistration$)
  [2018-12-19 14:46:43,586] INFO starting (kafka.server.KafkaServer)
  [2018-12-19 14:46:43,588] INFO Connecting to zookeeper on localhost:2181 (kafka.server.KafkaServer)

  [2018-12-19 14:46:48,112] INFO Kafka version : 1.1.0 (org.apache.kafka.common.utils.AppInfoParser)
  [2018-12-19 14:46:48,112] INFO Kafka commitId : fdcf75ea326b8e07 (org.apache.kafka.common.utils.AppInfoParser)
  [2018-12-19 14:46:48,121] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)

.. end

3-创建TOPIC
-----------
再启动一个shell终端，使用如下指令创建名为 ``test`` 的单分区单副本主题：

.. code-block:: console

  root@ubuntu:/opt# cd kafka/
  root@ubuntu:/opt/kafka# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
  Created topic "test".

.. end

可以通过命令行查看已创建的TOPIC.

.. code-block:: console

  root@ubuntu:/opt/kafka# bin/kafka-topics.sh --list --zookeeper localhost:2181
  test

.. end

4-发送消息
----------
Kafka自带一个命令行客户机，它将从文件或标准输入中获取输入，并将其作为消息发送到Kafka集群。默认情况下，每一行都将作为单独的消息发送。
使用如下指令发送消息。

.. code-block:: console

  root@ubuntu:/opt/kafka# bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
  >this is the first message
  >can you receive^[[D?
  >

.. end

5-启动消费者
------------
同样，KAFKA可以使用命令行启动消费者服务。再启动一个shell终端，执行如下命令：

.. code-block:: console

  cecgw@ubuntu:/opt/kafka$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
  this is the first message
  can you receiv?

.. end

目前已经正常收到消息。

6-设置多代理集群 (multi-broker cluster)
---------------------------------------
在同一个物理服务器下，启动多代理模式。
首先为每个代理创建配置文件。

.. code-block:: console

  root@ubuntu:/opt/kafka/config# cp server.properties server-1.properties 
  root@ubuntu:/opt/kafka/config# cp server.properties server-2.properties

.. end

编辑配置文件如下：

.. code-block:: console

  config/server-1.properties:
      broker.id=1
      listeners=PLAINTEXT://:9093
      log.dirs=/tmp/kafka-logs-1
 
  config/server-2.properties:
      broker.id=2
      listeners=PLAINTEXT://:9094
      log.dirs=/tmp/kafka-logs-2

.. end

.. Note::

  broker.id 是唯一标识，在同一个物理服务器上，端口不能重复使用，在进行配置时注意。

.. end

我们已经有了Zookeeper，我们的单节点已经启动，所以我们只需要启动两个新节点:

.. code-block:: console

  root@ubuntu:/opt/kafka# bin/kafka-server-start.sh config/server-1.properties &
  [1] 7869
  root@ubuntu:/opt/kafka# [2018-12-19 15:14:06,475] INFO Registered kafka:type=kafka.Log4jController MBean (kafka.utils.Log4jControllerRegistration$)

  root@ubuntu:/opt/kafka#bin/kafka-server-start.sh config/server-2.properties &

.. end

创建一个复制因子为3的主题：

.. code-block:: console

  > bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic

.. end
   
