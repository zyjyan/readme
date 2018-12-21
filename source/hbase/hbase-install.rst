HBASE安装
~~~~~~~~~~~~~~~~~~~~~

单机版本安装
------------
安装环境：ubuntu_16.04+java_8u191
前置条件：安装java hadoop-2.7.7
对于版本的选择，`请查看官方说明文档。<http://hbase.apache.org/book.html>`_.
HBase单机伪分布式模式配置安装步骤,以hadoop为存储底层。
1.获取软件安装包。`点此获得软件包下载地址 <https://hbase.apache.org/downloads.html>`_.
2.解压软件包到安装目录，设置的安装目录为/opt。

.. code-block:: console

 root@ubuntu:/opt# tar -xzvf hbase-2.1.1-bin.tar.gz -C /opt
 root@ubuntu:/opt# mv hbase-2.1.1/ hbase

.. end

3. 给文件赋予访问权限.前提，已经创建hadoop用户。
.. code-block:: console
 
 chown -R hadoop:hadoop hbase

.. end

4. 配置环境变量。并使配置生效。

.. code-block:: console

  # vi /home/hadoop/.bashrc # 在文件末尾追加：
  export PATH=$PATH:/opt/hbase/bin
  # source /home/hadoop/.bashrc

.. end
查看HBase版本，确定HBase是否安装成功，命令如下：

.. code-block:: console
	
    hadoop@ubuntu:/opt/hbase$ hbase version
	Error: Could not find or load main class org.apache.hadoop.hbase.util.GetJavaProperty
	SLF4J: Class path contains multiple SLF4J bindings.
	SLF4J: Found binding in [jar:file:/opt/hadoop-2.7.7/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/opt/hbase/lib/client-facing-thirdparty/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
	SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
	HBase 2.1.1
	Source code repository git://kalashnikov.local/Users/stack/checkouts/hbase revision=b60a92d6864ef27295027f5961cb46f9162d7637
	Compiled by stack on Fri Oct 26 19:27:03 PDT 2018
	From source with checksum 25696727867a6ae76c16a9bfb05ac677

.. end

配置/opt/hbase/conf/hbase-env.sh,配置JAVA_HOME，HBASE_CLASSPATH，HBASE_MANAGES_ZK.
HBASE_CLASSPATH设置为本机Hadoop安装目录下的conf目录（即/opt/hadoop-2.7.7/etc/hadoop），在该文件的最前面，添加如下三行内容：

.. code-block:: console

 export JAVA_HOME=/usr/lib/jdk
 export HBASE_CLASSPATH=/opt/hadoop-2.7.7/etc/hadoop 
 export HBASE_MANAGES_ZK=true

.. end

配置/opt/hbase/conf/hbase-site.xml，添加信息如下：

.. code-block:: console

 <configuration>
        <property>
                <name>hbase.rootdir</name>
                <value>hdfs://localhost:9000/hbase</value>
        </property>
        <property>
                <name>hbase.cluster.distributed</name>
                <value>true</value>
        </property>
 </configuration>
.. end


5. 启动hbase,并查看hadoop存储变化。

.. code-block:: console

  hadoop@ubuntu:/opt/hbase$ ./bin/start-hbase.sh 
  hadoop@ubuntu:/opt/hbase$ jps
	2944 NameNode
	3301 SecondaryNameNode
	5704 HQuorumPeer
	4152 NodeManager
	5769 HMaster
	5897 HRegionServer
	4029 ResourceManager
	6558 Jps
	3103 DataNode
  # 查看hadoop 文件目录.
	hadoop@ubuntu:/opt/hbase$ hdfs dfs -ls /
	Found 2 items
	drwxr-xr-x   - hadoop supergroup          0 2018-12-21 16:08 /hbase
	drwxr-xr-x   - hadoop supergroup          0 2018-12-21 15:39 /user
	hadoop@ubuntu:/opt/hbase$
  # 可以看出，已经在hadoop下创建了hbase目录。权限为hadoop。
.. end

至此，单机版完成安装。

分布式安装
----------









.. Note::
   
   Pre-version。
