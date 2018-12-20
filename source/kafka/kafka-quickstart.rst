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


在目录 ``/var/log/docker/`` 目录下，卸载后仍然保留原有的镜像，容器，以及网络的配置，现在Docker CE包已经改名为 ``docker-ce``.

安装Docker 
----------
安装方式包括两种，一种为APT安装，一种是通过DEB包安装。

APT安装



deb包安装


用户可以到 `Docker源选取合适的包下载安装 <https://download.docker.com/linux/ubuntu/dists/trusty/>`_.
下载后，使用如下命令进行安装：
.. code-block:: console
   root@cecgw:/home/cecgw# dpkg -i docker-ce_17.12.0~ce-0~ubuntu_amd64.deb
.. end
首次执行后，发现出现报错信息如下:

.. code-block:: console

  root@cecgw:/home/cecgw#  dpkg -i docker-ce_17.12.0~ce-0~ubuntu_amd64.deb 
  (Reading database ... 134914 files and directories currently installed.)
  Preparing to unpack docker-ce_17.12.0~ce-0~ubuntu_amd64.deb ...
  Unpacking docker-ce (17.12.0~ce-0~ubuntu) over (17.12.0~ce-0~ubuntu) ...
  dpkg: dependency problems prevent configuration of docker-ce:
  docker-ce depends on libsystemd-journal0 (>= 201); however:
  Package libsystemd-journal0 is not installed.

  dpkg: error processing package docker-ce (--install):
  dependency problems - leaving unconfigured
  Processing triggers for ureadahead (0.100.0-16) ...
  Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
  Errors were encountered while processing:
  docker-ce
.. end
发现系统中缺少依赖，关于依赖的问题，确实是在软件部署及升级过程中，非常头痛的问题，Docker也是在着重解决该问题。

.. Note::

  这里给大家普及下，如何找到相关的依赖，`站点 https://pkgs.org/ <https://pkgs.org/>`_ 中提供了linux中大部分的软件包，
  大家可以在此网站上，选取适合自己的操作系统版本，并搜索下载相关的软件包。
.. end


下载完成后，先安装相关的依赖，然后完成Docker相关的软件安装即可。我们查看系统用户组，`/etc/group`发现多出系统用户组docker，但查看`/etc/passwd`，并没有发现多出docker用户。

下载完成后，我们可以使用如下命令验证Docker安装是否成功：

.. code-block:: console


        docker run hello-world
	Unable to find image 'hello-world:latest' locally
	latest: Pulling from library/hello-world
	ca4f61b1923c: Pull complete 
	Digest: sha256:66ef312bbac49c39a89aa9bcc3cb4f3c9e7de3788c944158df3ee0176d32b751
	Status: Downloaded newer image for hello-world:latest

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
	 https://cloud.docker.com/

	For more examples and ideas, visit:
	 https://docs.docker.com/engine/userguide/
.. end

该命令，将下载一个测试镜像，并且启动容器；该测试容器将打印Hello from Docker. 并且退出，似乎我们已经运行了一个容器，但对于其原理及用途扔不是很清晰。带着疑问继续研究。
具体到Docker自身，我们需要观察，Docker运行的一些基本元素，比如，是否只有root用户权限可操作？是否绑定系统端口？是否跟随系统自启动？等一系列的问题，这个将在随后的章节给出答案。

卸载DOCKER CE
-------------
1. 卸载相关软件包:

.. code-block:: console

  # sudo apt-get purge docker-ce

.. end

2. 删除相关的镜像，容器，卷：

.. code-block:: console

  # sudo rm -rf /var/lib/docker

.. end

		 

.. figure:: image/docker/docker-1.png
   :width: 80%
   :align: center
   :alt: Docker-1

end-21

   
