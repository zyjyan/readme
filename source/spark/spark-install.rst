SPARK安装
~~~~~~~~~~~~~~~~~~~~~

单机版本安装
------------
安装环境：ubuntu_16.04+scala_2.12+java_8u191
前置条件：安装java hadoop scala
单机版本安装步骤
1.获取软件安装包。`点此获得软件包下载地址 <http://spark.apache.org/downloads.html>`_.
.. note::
 官方网址提供了如下软件下载版本:
 Source code: Spark 源码，需要编译才能使用；
 Pre-build with user-provided Hadoop: 属于“Hadoop free”版，可应用到任意Hadoop 版本；
 Pre-build for Hadoop 2.7 and later: 基于Hadoop 2.7的预先编译版，需要与本机安装的Hadoop版本对应，可选的还有Hadoop 2.6、Hadoop 2.4、Hadoop 2.3、Hadoop1.x、CDH4。
 Pre-build with scala 2.12 and user-provide Apache Hadoop.
 前面已经自己安装了Hadoop和scala，所以，在“Choose a package type”后面需要选择 "Pre-build with scala 2.12 and user-provide Apache Hadoop."，因为这个选项的Spark可以应用于任意Hadoop版本，所以，就可以应用到我们已经安装的Hadoop版本。然后，点击“Download Spark”后面的"spark-2.4.0-bin-without-hadoop-scala-2.12.tgz"下载即可。
 Spark部署模式主要有四种：Local模式（单机模式）、Standalone模式（使用Spark自带的简单集群管理器）、YARN模式（使用YARN作为集群管理器）和Mesos模式（使用Mesos作为集群管理器）。
 这里介绍Local模式（单机模式）的 Spark安装。我们选择Spark 1.6.2版本，并且假设当前使用用户名hadoop登录了Linux操作系统。
.. end

2.解压软件包到安装目录，设置的安装目录为/opt。

.. code-block:: console
 # tar -xzvf scala-2.12.1.tgz -C /opt
 # mv scala-2.12.1.tgz scala
.. end

3. 给文件赋予访问权限.前提，已经创建hadoop用户。
.. code-block:: console
  chown -R hadoop:hadoop scala

.. end

4. 配置环境变量。并使配置生效。

.. code-block:: console

  # vi /home/hadoop/.bashrc # 在文件末尾追加：
    export SCALA_HOME=/opt/scala
    export PATH=.:$PATH:$JAVA_HOME/bin:$SCALA_HOME/bin
  # source /home/hadoop/.bashrc

.. end

5. 验证测试.

.. code-block:: console

  hadoop@ubuntu:/opt$ scala
  Welcome to Scala 2.12.1 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_191).
  Type in expressions for evaluation. Or try :help.

  scala> 4+5
  res0: Int = 9

  scala> :quit
  hadoop@ubuntu:/opt$ scala -version
  Scala code runner version 2.12.1 -- Copyright 2002-2016, LAMP/EPFL and Lightbend, Inc.

.. end
至此，单机版完成安装。

分布式安装
----------









.. Note::
   
   Pre-version。
