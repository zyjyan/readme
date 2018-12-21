HADOOP安装
~~~~~~~~~~

单机版本安装
------------
操作系统：ubuntu-16.04
jdk版本：jdk-8u191-linux-x64.tar.gz
hadoop版本：2.7.7
关于hadoop与jdk之间的关系，可以参考（https://wiki.apache.org/hadoop/HadoopJavaVersions）

创建hadoop用户
=============

使用root登陆系统，创建用户组：hadoop，然后在此用户组下创建hadoop用户。可在安装系统的时候创建，也可以在安装好之后用如下命令创建：

.. code-block:: console

 添加用户：# sudo useradd -m hadoop -s /bin/bash
 设置密码：# passwd hadoop 
 设置root权限：# sudo adduser hadoop sudo。

.. end

java安装
========
1. 下载jdk软件包。jdk下载地址为：(https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
选择所需要的软件包，本次安装环境为ubuntu1604，选择软件包版本为：jdk-8u191-linux-x64.tar.gz

.. figure:: image/jdk-choose.png
   :width: 80%
   :align: center
   :alt: jdk-choose

2. 将下载的压缩包，传输至安装环境。解压。

.. code-block:: console

 # tar -xzvf jdk-8u191-linux-x64.tar.gz 解压
 # mv jdk1.8.0_191/ /usr/lib/jdk 将解压文件移动至/usr/lib/jdk，作为jdk安装目录。
 
.. end

3. 配置环境变量。

.. code-block:: console

 # vi /etc/profile 配置全局环境变量
 写入：
 # set java environment 
 export JAVA_HOME=/usr/lib/jdk
 export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
 export PATH=.:$JAVA_HOME/bin:$PATH

.. end

4. 测试验证。

.. code-block:: console

 # java -version 测试
 java version "1.8.0_191"
 Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
 Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)

 NOTE: 切换到hadoop 用户，测试java是否正常，直接执行java -version 提示需要安装java，需要执行 source /etc/profile 即可。
 在不执行 /etc/profile 情况下，hadoop不能读取环境变量，该情况下，修改/home/cecgw/.bashrc文件，追加上述环境变量，source /home/hadoop/.bashrc 即可在hadoop用户下永久生效。
 如果只允许某个用户使用java，则只需要在该用户的主目录下，修改.bashrc，在文件末尾追加上述环境变量即可。
 
.. end

至此，java安装完毕。

hadoop 单机伪分布式安装
=======================

1. 软件包下载.

软件包下载：软件下载地址为 (http://mirrors.shu.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz)

2. 解压缩文件.

解压到安装目录(root 用户),安装软件。

.. code-block:: console

 # tar -xzvf hadoop-2.9.0.tar.gz -C /opt
 # cd /opt 
 # chown -R hadoop:hadoop hadoop-2.9.0
 Hadoop 解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示 Hadoop 版本信息：
 # cd /opt/hadoop-2.9.0
 # ./bin/hadoop version

.. end

3. 配置hadoop环境变量

.. code-block:: console

 vi /home/hadoop/.bashrc
 追加 export PATH=$PATH:/opt/hadoop-2.9.0/sbin:/opt/hadoop-2.9.0/bin
 使配置生效：
 # source /home/hadoop/.bashrc

.. end

4. 配置hadoop 配置文件

Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。

Hadoop 的配置文件位于 /opt/hadoop-2.7.7/etc/hadoop/ 中，伪分布式需要修改2个配置文件 core-site.xml 和 hdfs-site.xml 。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现。

.. code-block:: console

	修改 core-site.xml
	修改配置文件 core-site.xml (通过 gedit 编辑会比较方便: gedit ./etc/hadoop/core-site.xml)，将当中的
	<configuration>
	</configuration>
	XML
	修改为下面配置：
	<configuration>
	<property>
	<name>hadoop.tmp.dir</name>
	<value>file:/usr/local/hadoop/tmp</value>
	<description>Abase for other temporary directories.</description>
	</property>
	<property>
	<name>fs.defaultFS</name>
	<value>hdfs://localhost:9000</value>
	</property>
	</configuration>
	XML
	同样的，修改配置文件 hdfs-site.xml：
	<configuration>
	<property>
	<name>dfs.replication</name>
	<value>1</value>
	</property>
	<property>
	<name>dfs.namenode.name.dir</name>
	<value>file:/usr/local/hadoop/tmp/dfs/name</value>
	</property>
	<property>
	<name>dfs.datanode.data.dir</name>
	<value>file:/usr/local/hadoop/tmp/dfs/data</value>
	</property>
	</configuration>
	修改
	./etc/hadoop/hadoop-env.sh 中设置 JAVA_HOME 变量，即在该文件中找到：
	export JAVA_HOME=${JAVA_HOME}
	将这一行改为JAVA安装位置：
	export JAVA_HOME=/usr/lib/jdk

.. end

5. 测试验证。

配置完成后，执行 NameNode 的格式化:

.. code-block:: console

 # ./bin/hdfs namenode -format

.. end

.. figure:: image/namenode-format.png
   :width: 80%
   :align: center
   :alt: jdk-choose

启动NameNode和DataNode守护进程。启动时，提示输入密码，输入hadoop密码即可。

.. code-block:: console

 # ./sbin/start-dfs.sh
 # jsp
 # 可以看到 namenode datanode secondnamenode 均正常启动。

.. end

成功启动后，可以访问 Web 界面 http://ip:50070 查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。

6. 配置免密码登录。

上述启动过程中，需要输入密码，可配置免密码登录，即可避免输入密码。

.. code-block:: console

 以hadoop用户登录，
 cd ~/.ssh/ # 若没有该目录，请先执行一次ssh localhost 
 ssh-keygen -t rsa # 会有提示，都按回车就可以
 cat ./id_rsa.pub >> ./authorized_keys # 加入授权
 这样下次启动时，则不需要再次输入密码启动hadoop.

7. 运行Hadoop伪分布式实例

.. code-block:: console

	要使用 HDFS，首先需要在 HDFS 中创建用户目录：
	./bin/hdfs dfs -mkdir -p  /user/hadoop
	Shell 命令
	接着将 ./etc/hadoop 中的 xml 文件作为输入文件复制到分布式文件系统中，即将 /usr/local/hadoop/etc/hadoop 复制到分布式文件系统中的 /user/hadoop/input 中。我们使用的是 hadoop 用户，并且已创建相应的用户目录 /user/hadoop ，因此在命令中就可以使用相对路径如 input，其对应的绝对路径就是 /user/hadoop/input:
	./bin/hdfs dfs -mkdir input
	./bin/hdfs dfs -put ./etc/hadoop/*.xml input
	Shell 命令
	复制完成后，可以通过如下命令查看文件列表：
	./bin/hdfs dfs -ls input
	Shell 命令
	伪分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步骤中创建的本地 input 文件夹，输出结果 output 文件夹都删掉来验证这一点）。
	./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'
	Shell 命令
	查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：
	./bin/hdfs dfs -cat output/*

.. end

8. 启动YARN

.. code-block:: console

	启动YARN
	（伪分布式不启动 YARN 也可以，一般不会影响程序执行）
	有的读者可能会疑惑，怎么启动 Hadoop 后，见不到书上所说的 JobTracker 和 TaskTracker，这是因为新版的 Hadoop 使用了新的 MapReduce 框架（MapReduce V2，也称为 YARN，Yet Another Resource Negotiator）。
	YARN 是从 MapReduce 中分离出来的，负责资源管理与任务调度。YARN 运行于 MapReduce 之上，提供了高可用性、高扩展性，YARN 的更多介绍在此不展开，有兴趣的可查阅相关资料。
	上述通过 ./sbin/start-dfs.sh 启动 Hadoop，仅仅是启动了 MapReduce 环境，我们可以启动 YARN ，让 YARN 来负责资源管理与任务调度。
	首先修改配置文件 mapred-site.xml，这边需要先进行重命名：
	mv ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml
	Shell 命令
	然后再进行编辑，同样使用 gedit 编辑会比较方便些 gedit ./etc/hadoop/mapred-site.xml ：
	<configuration>
	<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
	</property>
	</configuration>
	XML
	接着修改配置文件 yarn-site.xml：
	<configuration>
	<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
	</property>
	</configuration>
	XML
	然后就可以启动 YARN 了（需要先执行过 ./sbin/start-dfs.sh）：
	./sbin/start-yarn.sh      # 启动YARN
	./sbin/mr-jobhistory-daemon.sh start historyserver  # 开启历史服务器，才能在Web中查看任务运行情况
	Shell 命令
	开启后通过 jps 查看，可以看到多了 NodeManager 和 ResourceManager 两个后台进程

.. end

启动 YARN 之后，运行实例的方法还是一样的，仅仅是资源管理方式、任务调度不同。观察日志信息可以发现，不启用 YARN 时，是 "mapred.LocalJobRunner"在跑任务，启用 YARN 之后，是"mapred.YARNRunner"在跑任务。启动 YARN 有个好处是可以通过 Web 界面查看任务的运行情况：http://ip:8088/cluster，。


