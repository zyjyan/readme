Flume-install
~~~~~~~~~~~~~

Flume安装
---------
1 前置条件

java需要提前安装。

Flume的安装非常简单，安装包下载地址为:http://www.apache.org/dyn/closer.lua/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz


.. code-block:: console

   tar -xzvf apache-flume-1.9.0-bin.tar.gz -C /opt/ #解压到指定文件
   cd /opt/ 
   ls
   mv apache-flume-1.9.0-bin/ flume #更改文件名
   cd flume/
   cd conf/
   cp flume-env.sh.template flume-env.sh
   vi flume-env.sh # 配置java环境变量
   root@master:/opt/flume# grep -vE  '^#|^$'  ./conf/flume-env.sh 
   export JAVA_HOME=/usr/lib/jdk 
   修改文件权限，使用hadoop用户运行flume
   chown -R hadoop:hadoop flume/
   # 验证
   hadoop-1@master:/opt/flume$ ./bin/flume-ng version
   Flume 1.9.0
   Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git
   Revision: d4fcab4f501d41597bc616921329a4339f73585e
   Compiled by fszabo on Mon Dec 17 20:45:25 CET 2018
   From source with checksum 35db629a3bda49d23e9b3690c80737f9

.. end




简单验证
--------

监控某一文件变化,并通过flume传输至hdfs.

1. 配置文件配置。

.. code-block:: console

  hadoop-1@master:/opt/flume/conf$ touch flume-conf-hdfs 

        a1.sources = r1
	a1.sinks = k1
	a1.channels = c1
	a1.sources.r1.type = exec
	a1.sources.r1.channels = c1
	a1.sources.r1.command = tail -f /home/hadoop-1/test.log
	a1.sinks.k1.type = hdfs
	a1.sinks.k1.channel = c1
	a1.sinks.k1.hdfs.path = hdfs://17.17.17.2:9000/user/flume # hadoop上该文件需要提前创建
	a1.sinks.k1.hdfs.roundValue = 10
	a1.sinks.k1.hdfs.roundUnit = minute
	a1.sinks.k1.hdfs.rollSize = 4000000
	a1.sinks.k1.hdfs.rollCount = 0
	a1.sinks.k1.hdfs.writeFormat = Text
	a1.sinks.k1.hdfs.fileType = DataStream
	a1.sinks.k1.hdfs.batchSize = 10
	a1.channels.c1.type = memory
	a1.channels.c1.capacity = 1000
	a1.channels.c1.transactionCapacity = 100

.. end

执行flume脚本。

.. code-block:: console

	hadoop-1@master:/opt/flume/bin$ ./flume-ng agent --conf ../conf/ --conf-file ../conf/flume-conf-hdfs --name a1 -Dflume.root.logger=INFO,console
	Info: Sourcing environment configuration script /opt/flume/conf/flume-env.sh
	Info: Including Hadoop libraries found via (/opt/hadoop-2.7.7/bin/hadoop) for HDFS access
	Info: Including HBASE libraries found via (/opt/hbase/bin/hbase) for HBASE access
	Error: Could not find or load main class org.apache.flume.tools.GetJavaProperty
	Error: Could not find or load main class org.apache.hadoop.hbase.util.GetJavaProperty
	Info: Including Hive libraries found via (/opt/hive) for Hive access
	+ exec /usr/lib/jdk/bin/java -Xmx20m -Dflume.root.logger=INFO,console -cp '/opt/flume/conf:/opt/flume/lib/*:/opt/hadoop-2.7.7/etc/hadoop:/opt/hadoop-2.7.7/share/hadoop/common/lib/*:/opt/hadoop-2.7.7/share/hadoop/common/*:/opt/hadoop-2.7.7/share/hadoop/hdfs:/opt/hadoop-2.7.7/share/hadoop/hdfs/lib/*:/opt/hadoop-2.7.7/share/hadoop/hdfs/*:/opt/hadoop-2.7.7/share/hadoop/yarn/lib/*:/opt/hadoop-2.7.7/share/hadoop/yarn/*:/opt/hadoop-2.7.7/share/hadoop/mapreduce/lib/*:/opt/hadoop-2.7.7/share/hadoop/mapreduce/*:/contrib/capacity-scheduler/*.jar:/opt/hbase/conf:/usr/lib/jdk/lib/tools.jar:/opt/hbase:/opt/hbase/lib/shaded-clients/hbase-shaded-client-byo-hadoop-2.1.1.jar:/opt/hbase/lib/client-facing-thirdparty/audience-annotations-0.5.0.jar:/opt/hbase/lib/client-facing-thirdparty/commons-logging-1.2.jar:/opt/hbase/lib/client-facing-thirdparty/findbugs-annotations-1.3.9-1.jar:/opt/hbase/lib/client-facing-thirdparty/htrace-core4-4.2.0-incubating.jar:/opt/hbase/lib/client-facing-thirdparty/log4j-1.2.17.jar:/opt/hbase/lib/client-facing-thirdparty/slf4j-api-1.7.25.jar:/opt/hadoop-2.7.7/etc/hadoop:/opt/hadoop-2.7.7/share/hadoop/common/lib/*:/opt/hadoop-2.7.7/share/hadoop/common/*:/opt/hadoop-2.7.7/share/hadoop/hdfs:/opt/hadoop-2.7.7/share/hadoop/hdfs/lib/*:/opt/hadoop-2.7.7/share/hadoop/hdfs/*:/opt/hadoop-2.7.7/share/hadoop/yarn/lib/*:/opt/hadoop-2.7.7/share/hadoop/yarn/*:/opt/hadoop-2.7.7/share/hadoop/mapreduce/lib/*:/opt/hadoop-2.7.7/share/hadoop/mapreduce/*:/contrib/capacity-scheduler/*.jar:/opt/hadoop-2.7.7/etc/hadoop:/opt/hbase/conf:/opt/hive/lib/*' -Djava.library.path=:/opt/hadoop-2.7.7/lib/native org.apache.flume.node.Application --conf-file ../conf/flume-conf-hdfs --name a1
	SLF4J: Class path contains multiple SLF4J bindings.
	SLF4J: Found binding in [jar:file:/opt/flume/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/opt/hadoop-2.7.7/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
	SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
	2019-03-26 11:00:24,660 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider.start(PollingPropertiesFileConfigurationProvider.java:62)] Configuration provider starting
	2019-03-26 11:00:24,770 (conf-file-poller-0) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:138)] Reloading configuration file:../conf/flume-conf-hdfs
	2019-03-26 11:00:24,916 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,921 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:c1
	2019-03-26 11:00:24,921 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:r1
	2019-03-26 11:00:24,921 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1117)] Added sinks: k1 Agent: a1
	2019-03-26 11:00:24,921 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,921 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,922 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,922 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,922 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,922 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:r1
	2019-03-26 11:00:24,922 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:r1
	2019-03-26 11:00:24,923 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:c1
	2019-03-26 11:00:24,923 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,923 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,931 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,932 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:c1
	2019-03-26 11:00:24,932 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addComponentConfig(FlumeConfiguration.java:1203)] Processing:k1
	2019-03-26 11:00:24,932 (conf-file-poller-0) [WARN - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.validateConfigFilterSet(FlumeConfiguration.java:623)] Agent configuration for 'a1' has no configfilters.
	2019-03-26 11:00:25,024 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:163)] Post-validation flume configuration contains configuration for agents: [a1]
	2019-03-26 11:00:25,025 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:151)] Creating channels
	2019-03-26 11:00:25,083 (conf-file-poller-0) [INFO - org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:42)] Creating instance of channel c1 type memory
	2019-03-26 11:00:25,138 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:205)] Created channel c1
	2019-03-26 11:00:25,139 (conf-file-poller-0) [INFO - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:41)] Creating instance of source r1, type exec
	2019-03-26 11:00:25,160 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:42)] Creating instance of sink: k1, type: hdfs
	2019-03-26 11:00:25,248 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:120)] Channel c1 connected to [r1, k1]
	2019-03-26 11:00:25,276 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:162)] Starting new configuration:{ sourceRunners:{r1=EventDrivenSourceRunner: { source:org.apache.flume.source.ExecSource{name:r1,state:IDLE} }} sinkRunners:{k1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@3f38314a counterGroup:{ name:null counters:{} } }} channels:{c1=org.apache.flume.channel.MemoryChannel{name: c1}} }
	2019-03-26 11:00:25,317 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:169)] Starting Channel c1
	2019-03-26 11:00:25,997 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)] Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.
	2019-03-26 11:00:25,999 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)] Component type: CHANNEL, name: c1 started
	2019-03-26 11:00:25,999 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:196)] Starting Sink k1
	2019-03-26 11:00:26,025 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:207)] Starting Source r1
	2019-03-26 11:00:26,105 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.ExecSource.start(ExecSource.java:170)] Exec source starting with command: tail -f /home/hadoop-1/test.log
	2019-03-26 11:00:26,106 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)] Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.
	2019-03-26 11:00:26,106 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)] Component type: SOURCE, name: r1 started
	2019-03-26 11:00:26,114 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)] Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.
	2019-03-26 11:00:26,114 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)] Component type: SINK, name: k1 started

.. end

写入字符到test.log,到hadoop上查看是否数据正确录入即可.

.. figure:: flume/image/flume/flume_data.png
   :width: 80%
   :align: center
   :alt: flume_data

.. note::

 本次的安装环境将source 定义为file文件的变化, sink为hdfs. flume在处理数据，向hdfs写入时，会用到hadoop相关的jar包，如果该jar包没有在安装flume的hosts上加载，在程序运行时，会报依赖错误。解决此问题可以将hadoop相关的安装包解压到某一目录下，在系统的环境变量中，设置hadoop 路径，即可解决此问题。比如使用hadoop用户运行flume, hadoop解压到/opt目录下，只需要在/home/hadoop/.bashrc下配置:
 export PATH=$PATH:/opt/hadoop-2.7.7/sbin:/opt/hadoop-2.7.7/bin
 export HADOOP_HOME=/opt/hadoop-2.7.7,然后执行 source /home/hadoop/.bashrc即可。

.. warning::

 当hadoop设置为HA模式时，需要将flume-conf-hdfs文件中a1.sinks.k1.hdfs.path = hdfs://17.17.17.2:9000/user/flume 修改为
 a1.sinks.k1.hdfs.path = hdfs://hadoop-cluster/user/flume,其中，hadoop-cluster为hadoop集群名称，为了解析该名称，需要将hadoop集群的core-site.xml以及hdfs-site.xml文件拷贝到flume的conf文件夹下，用于解析该集群名称。
