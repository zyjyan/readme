SCALA安装
~~~~~~~~~~~~~~~~~~~~~

单机版本安装
------------
安装环境：ubuntu_16.04+scala_2.12+java_8u191
前置条件：安装java hadoop
单机版本安装步骤
1.获取软件安装包。`点此获得软件包下载地址 <https://www.scala-lang.org/download/>`_.
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
