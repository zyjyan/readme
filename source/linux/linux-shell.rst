	Linux-Shell
~~~~~~~~~~~

修改Ubuntu网卡名称
------------------

.. code-block:: console

 vi nano /etc/default/grub
 找到GRUB_CMDLINE_LINUX=""
 改为GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"
 然后sudo grub-mkconfig -o /boot/grub/grub.cfg
 重启后，网卡名称果然变成了eth0和wlan0
 修改后重启即可；

.. end


awk
---

.. code-block:: console

 for i in `ps -aux|grep ping|awk '{print $2}'`;  do kill -9 $i; done; 

.. end

.. figure:: image/linux-shell/awk.png
   :width: 80%
   :align: center
   :alt: awk


find
----

.. code-block:: console

 # find /  -name "cecgw"

.. end

du
--

.. code-block:: console

 查看目录大小
 # du  -h --max-depth=1 /home/cecgw

.. end


linux用户和组
-------------

  Linux 是个多用户多任务的分时操作系统，所有想要调用系统资源的用户都必须先向系统管理员申请一个账号，
然后通过这个账号进入系统。用户的账号一方面能帮助系统管理员对使用系统的用户进行跟踪，控制他们对系统资源的访问；
另一方面也能帮助用户组织文件，为用户提供安全性保护。每个账号都拥有一个唯一的用户名和用户密码。
用户在登录时键入正确的用户名和密码后，才能进入系统和自己的主目录。
一个用户可以存在于多个用户组中。



1、查看用户信息。

.. code-block:: console

 root@ubuntu:~# vi /etc/passwd
 root:x:0:0:root:/root:/bin/bash
 daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
 bin:x:2:2:bin:/bin:/usr/sbin/nologin
 sys:x:3:3:sys:/dev:/usr/sbin/nologin
 sync:x:4:65534:sync:/bin:/bin/sync
 games:x:5:60:games:/usr/games:/usr/sbin/nologin
 man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
 lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
 mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
 news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
 uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
 proxy:x:13:13:proxy:/bin:/usr/sbin/nologin

 文件中每行记录用冒号 : 分隔为 7 个字段，从左到右具体含义是：
 用户名：密码占位符（x 表示用户需要密码登录）：用户标识号（UID）：组标识号（GID）：注释性描述：主目录：登录的 shell

.. end

2、查看密码文件。

.. code-block:: console

 root@ubuntu:~# vi /etc/shadow
 root:!:17882:0:99999:7:::
 daemon:*:17001:0:99999:7:::
 bin:*:17001:0:99999:7:::
 sys:*:17001:0:99999:7:::
 sync:*:17001:0:99999:7:::
 games:*:17001:0:99999:7:::
 man:*:17001:0:99999:7:::
 lp:*:17001:0:99999:7:::
 mail:*:17001:0:99999:7:::

 文件中每行记录用冒号 : 分隔为 9 个字段，从左到右具体含义是：
 用户名：加密口令：最后一次修改时间：最短有效天数：最长有效天数：过期前的警告时间：不活动时间：用户失效时间：暂时保留未使用

.. end

3、查看组信息文件。

.. code-block:: console

 root@ubuntu:~# vi /etc/group
 root:x:0:
 daemon:x:1:
 bin:x:2:
 sys:x:3:
 adm:x:4:syslog,cecgw
 tty:x:5:
 disk:x:6:
 lp:x:7:
 mail:x:8:
 
 用户组的所有信息都存放在/etc/group文件中。将用户分组是Linux 系统中对用户进行管理及控制访问权限的一种手段。

 每个用户都属于某个用户组；一个组中可以有多个用户，一个用户也可以属于不同的组。
 当一个用户同时是多个组中的成员时，在/etc/passwd文件中记录的是用户所属的主组，也就是登录时所属的默认组，而其他组称为附加组。
  
 用户要访问属于附加组的文件时，必须首先使用newgrp命令使自己成为所要访问的组中的成员。
 用户组的所有信息都存放在/etc/group文件中。此文件的格式也类似于/etc/passwd文件，由冒号(:)隔开若干个字段，这些字段有：
 组名:口令:组标识号:组内用户列表
 "组名"是用户组的名称，由字母或数字构成。与/etc/passwd中的登录名一样，组名不能重复。
 "口令"字段存放的是用户组加密后的口令字。一般Linux 系统的用户组都没有口令，即这个字段一般为空，或者是*。
 "组标识号"与用户标识号类似，也是一个整数，被系统内部用来标识组。

.. end



lsof
----

linux哲学为“一切皆文件”. linux下文件类型包括：
1普通文件
2目录
3符号链接
4面向块的设备文件
5面向字符的设备文件
6管道和命名管道
7套接字

lsof是list open files的简称。

1、 查看当前所有打开文件。


.. code-block:: console

 root@ubuntu:/home/cecgw/readme/source# lsof|more
 COMMAND    PID  TID             USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
 systemd      1                  root  cwd       DIR              252,0     4096          2 /
 systemd      1                  root  rtd       DIR              252,0     4096          2 /
 systemd      1                  root  txt       REG              252,0  1581360     131115 /lib/systemd/systemd
 systemd      1                  root  mem       REG              252,0    18976     131700 /lib/x86_64-linux-gnu/libuuid.so.1.3.0
 systemd      1                  root  mem       REG              252,0   262408     131575 /lib/x86_64-linux-gnu/libblkid.so.1.1.0
 systemd      1                  root  mem       REG              252,0    14608     135552 /lib/x86_64-linux-gnu/libdl-2.23.so
 lsof显示的结果，从左往右分别代表：打开该文件的程序名，进程id，用户，文件描述符，文件类型，设备，大小，iNode号，文件名。

.. end

2、列出被删除但占用空间的文件

.. code-block:: console

 root@ubuntu:~# lsof|grep deleted

.. end

3、恢复打开但被删除的文件。

以/home/cecgw/abc.txt 文件为例，通过linux窗口1打开该文件，然后通过另外一个窗口删除它(root用户)：

.. code-block:: console

 root@ubuntu:/home/cecgw# lsof|grep abc.txt
 vi        3025                  root    4u      REG              252,0    12288    4198167 /home/cecgw/.abc.txt.swp

 可以找到进程id为3025的进程打开了该文件，每个进程在/proc下都有文件描述符打开的记录：

 root@ubuntu:/home/cecgw# ls -l /proc/3025/fd
 total 0
 lrwx------ 1 root root 64 May 23 14:49 0 -> /dev/pts/1
 lrwx------ 1 root root 64 May 23 14:49 1 -> /dev/pts/1
 lrwx------ 1 root root 64 May 23 14:49 2 -> /dev/pts/1
 lrwx------ 1 root root 64 May 23 14:49 4 -> /home/cecgw/.abc.txt.swp

 这里就找到了被删除的abc.txt文件,文件描述符是4，我们把它重定向出来：


.. end

.. figure:: image/linux-shell/awk.png
   :width: 80%
   :align: center
   :alt: awk


4、查看某个端口被占用情况

.. code-block:: console

 root@master:/home/ubuntu# lsof -i :50070
 COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
 java    28399 hadoop-1  193u  IPv4 130015      0t0  TCP master:50070 (LISTEN)

 可以看到是pid 28399 占用了该端口。通过 ps -aux|grep 28399即可看到详细进程信息。

.. end

5、查看当前文件被哪些进程打开

.. code-block:: console

 root@master:/var/log# lsof syslog
 COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
 rsyslogd 1014 syslog    6w   REG  252,0     6591 131316 syslog

.. end

6、查看某个目录下那些文件被打开

.. code-block:: console

 root@master:/var/log# lsof +D ./
 OMMAND    PID   USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
 syslogd  1014 syslog    5w   REG  252,0    73859 132249 ./auth.log
 syslogd  1014 syslog    6w   REG  252,0     6591 131316 ./syslog
 syslogd  1014 syslog    7w   REG  252,0    58495 132182 ./kern.log
 bash     13646   root  cwd    DIR  252,0     4096 135029 .
 lsof     13811   root  cwd    DIR  252,0     4096 135029 .
 lsof     13812   root  cwd    DIR  252,0     4096 135029 .

.. end

7、 查看当前进程打开了哪些文件。

.. code-block:: console

 root@master:/var/log# lsof -p 28399
 COMMAND   PID     USER   FD      TYPE             DEVICE  SIZE/OFF    NODE NAME
 java    28399 hadoop-1  cwd       DIR              252,0      4096  919924 /opt/hadoop-2.7.7
 java    28399 hadoop-1  rtd       DIR              252,0      4096       2 /
 java    28399 hadoop-1  txt       REG              252,0      8464  528753 /usr/lib/jdk/bin/java
 java    28399 hadoop-1  mem       REG              252,0      6254  920323 /opt/hadoop-2.7.7/share/hadoop/hdfs/webapps/hdfs/explorer.js
 java    28399 hadoop-1  mem       REG              252,0      6249  920324 /opt/hadoop-2.7.7/share/hadoop/hdfs/webapps/hdfs/explorer.html
 java    28399 hadoop-1  mem       REG              252,0   3135615  917824 /usr/lib/jdk/jre/lib/charsets.jar
 java    28399 hadoop-1  mem       REG              252,0   3505311  917864 /usr/lib/jdk/jre/lib/resources.jar
 java    28399 hadoop-1  mem       REG              252,0     11300  920328 /opt/hadoop-2.7.7/share/hadoop/hdfs/webapps/hdfs/dfshealth.js
 java    28399 hadoop-1  mem       REG              252,0     13529  920327 /opt/hadoop-2.7.7/share/hadoop/hdfs/webapps/hdfs/dfshealth.html
 java    28399 hadoop-1  mem       REG              252,0   1178932  917894 /usr/lib/jdk/jre/lib/ext/localedata.jar
 java    28399 hadoop-1  mem       REG              252,0   3860502  917887 /usr/lib/jdk/jre/lib/ext/cldrdata.jar

.. end

8、 根据进程pid 查看端口。

.. code-block:: console

 root@master:/var/log# lsof -i|grep 28399
 java     28399 hadoop-1  193u  IPv4 130015      0t0  TCP master:50070 (LISTEN)
 java     28399 hadoop-1  215u  IPv4 130027      0t0  TCP master:9000 (LISTEN)
 java     28399 hadoop-1  217u  IPv4 203959      0t0  TCP master:9000->slaver-1:60934 (ESTABLISHED)
 java     28399 hadoop-1  218u  IPv4 203961      0t0  TCP master:9000->slaver-3:36628 (ESTABLISHED)
 java     28399 hadoop-1  219u  IPv4 203963      0t0  TCP master:9000->slaver-2:38876 (ESTABLISHED)

.. end

ubuntu linux-18.04制作本地源
----------------------------

.. note::
从Ubuntu 16.04 (xenial)版本开始，在将本地deb软件包创建repo时候，跟14.04以前的版本相比，强制要求gpg对Release文件签名，否则无法使用。

1、在能够联网的ubuntu 18.04服务器上安装密钥生成软件，为了提升下载速度，可以将ubuntu默认源修改为清华源。(vi /etc/apt/source.list exe-->:%s/hk.archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/gc)

.. code-block:: console

	root@ubuntu:/etc/apt# apt-get update
	root@ubuntu:/etc/apt# apt-get install gnupg rng-tools


.. end

2、下载相关的软件包。

.. code-block:: console

	root@ubuntu:/etc/apt# apt-get dist-upgrade
	root@ubuntu:/etc/apt# apt-get install python syslog-ng tree ruby-sass ntp mariadb-server


.. end

3、将下载的软件包拷贝到离线源目录。


.. code-block:: console

	root@ubuntu:~# mkdir packs
	root@ubuntu:~# mv /var/cache/apt/archives/*.deb packs/
	root@ubuntu:~# mv /var/cache/apt/archives/partial/*.deb packs/


.. end

4、生成公私钥对。

.. code-block:: console

	root@ubuntu:~# gpg --gen-key
	gpg (GnuPG) 2.2.4; Copyright (C) 2017 Free Software Foundation, Inc.
	This is free software: you are free to change and redistribute it.
	There is NO WARRANTY, to the extent permitted by law.

	Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

	GnuPG needs to construct a user ID to identify your key.

	Real name: cecgw
	Email address: cecgw@cecgw.cn
	You selected this USER-ID:
		"cecgw <cecgw@cecgw.cn>"

	Change (N)ame, (E)mail, or (O)kay/(Q)uit? O
	We need to generate a lot of random bytes. It is a good idea to perform
	some other action (type on the keyboard, move the mouse, utilize the
	disks) during the prime generation; this gives the random number
	generator a better chance to gain enough entropy.
	We need to generate a lot of random bytes. It is a good idea to perform
	some other action (type on the keyboard, move the mouse, utilize the
	disks) during the prime generation; this gives the random number
	generator a better chance to gain enough entropy.
	gpg: /root/.gnupg/trustdb.gpg: trustdb created
	gpg: key 3DCD44A298D1963A marked as ultimately trusted
	gpg: directory '/root/.gnupg/openpgp-revocs.d' created
	gpg: revocation certificate stored as '/root/.gnupg/openpgp-revocs.d/2C7AF0C89E56F1796EAA4C183DCD44A298D1963A.rev'
	public and secret key created and signed.

	pub   rsa3072 2019-09-30 [SC] [expires: 2021-09-29]
		  2C7AF0C89E56F1796EAA4C183DCD44A298D1963A
	uid                      cecgw <cecgw@cecgw.cn>
	sub   rsa3072 2019-09-30 [E] [expires: 2021-09-29]

	
.. end

执行gpg会进入一些对话，其中要新建一个用户名username和相应的密码。结束之后，输入命令，可以查看key：


.. code-block:: console

	root@ubuntu:~# gpg --list-key
	gpg: checking the trustdb
	gpg: marginals needed: 3  completes needed: 1  trust model: pgp
	gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
	gpg: next trustdb check due at 2021-09-29
	/root/.gnupg/pubring.kbx
	------------------------
	pub   rsa3072 2019-09-30 [SC] [expires: 2021-09-29]
		  2C7AF0C89E56F1796EAA4C183DCD44A298D1963A
	uid           [ultimate] cecgw <cecgw@cecgw.cn>
	sub   rsa3072 2019-09-30 [E] [expires: 2021-09-29]


.. end

导出公私钥对。

.. code-block:: console

	# 公钥
	root@ubuntu:~# gpg -a --export cecgw> cecgw.pub
	root@ubuntu:~# ls
	cecgw.pub  packs
    # 私钥
	root@ubuntu:~# gpg -a --export-secret-keys cecgw > cecgw.sec
	root@ubuntu:~# ls
	cecgw.pub  cecgw.sec  
	
.. end

上述完成制作本地源的基本准备工作。

5、进入准备好的deb包目录生成索引文件及Release文件和签名文件。	

.. code-block:: console

	root@ubuntu:~/packs# apt-ftparchive packages . > Packages
	root@ubuntu:~/packs# gzip -c Packages > Packages.gz
	root@ubuntu:~/packs# apt-ftparchive release . > Release
	root@ubuntu:~/packs# gpg --clearsign -o InRelease Release 
	root@ubuntu:~/packs# gpg -abs -o Release.gpg Release 
	root@ubuntu:~/packs# cp /root/cecgw.pub . # 将生成的公钥拷贝到离线文件夹中。
	
	
	
.. end


6、打包本地源。

.. code-block:: console

	root@ubuntu:~# tar -czvf packs.tar.gz packs/

.. end

至此我们得到了packs.tar.gz 离线源文件。

7、使用。在新的ubuntu18.04离线电脑上，将packs.tar.gz拷贝到该电脑中,以解压到/tmp文件为例.

.. code-block:: console

	root@ubuntu:/home/cecgw# tar -xzvf packs.tar.gz -C /tmp/

.. end

修改apt source 文件源为本地源.

.. code-block:: console

	root@ubuntu:/etc/apt# mv sources.list sources.list_org
	root@ubuntu:/etc/apt# touch sources.list
	root@ubuntu:/etc/apt# vi sources.list
	root@ubuntu:/etc/apt# echo 'deb file:///tmp/packs/ /' > /etc/apt/sources.list

.. end

添加认证公钥。

.. code-block:: console

	root@ubuntu:/tmp/packs# cd /tmp/packs/
	root@ubuntu:/tmp/packs# apt-key add cecgw.pub
	OK

.. end

这样我们即可在本地使用该离线源。

.. code-block:: console

	root@ubuntu:/tmp/packs# apt-get update
	Get:1 file:/tmp/packs  InRelease [1,524 B]
	Get:1 file:/tmp/packs  InRelease [1,524 B]
	Get:2 file:/tmp/packs  Packages [365 kB]
	Reading package lists... Done 
	root@ubuntu:/tmp/packs# apt-get install python
	Reading package lists... Done
	Building dependency tree       
	Reading state information... Done
	The following additional packages will be installed:
	  libpython-stdlib libpython2.7-minimal libpython2.7-stdlib python-minimal python2.7 python2.7-minimal
	Suggested packages:
	  python-doc python-tk python2.7-doc binutils binfmt-support
	The following NEW packages will be installed:
	  libpython-stdlib libpython2.7-minimal libpython2.7-stdlib python python-minimal python2.7 python2.7-minimal
	0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
	Need to get 0 B/3,958 kB of archives.
	After this operation, 16.8 MB of additional disk space will be used.

.. end




.. Note::
   
   For KAFKA。
