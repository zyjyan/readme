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
        root@ubuntu:/etc/apt# rngd -r /dev/urandom
        root@ubuntu:/etc/apt# watch -n 1 cat /proc/sys/kernel/random/entropy_avail 


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

Linux文件权限(V-STW)
--------------------
Linux操作系统是多任务(Multi-tasks)多用户(Multi-users)分时操作系统.这意味着系统中可以允许多个用户进行登录，并且允许多个用户在同一个操作系统中同时进行多种任务。那么这里就涉及到文件权限问题。linux系统中采用了用户和用户组的管理机制。


文件基本权限
============ 

.. code-block:: console


	cecgw@ubuntu:~/test$ ls -lh /dev/
	total 0
	crw-------  1 root  root     10, 175 Oct  4 18:24 agpgart
	crw-------  1 root  root     10, 235 Oct  4 18:24 autofs
	drwxr-xr-x  2 root  root         660 Oct  4 18:24 block
	drwxr-xr-x  2 root  root          80 Oct  4 18:24 bsg
	crw-rw----  1 root  disk     10, 234 Oct  4 18:24 btrfs-control
	drwxr-xr-x  3 root  root          60 Oct  4 18:24 bus
	lrwxrwxrwx  1 root  root           3 Oct  4 18:24 cdrom -> sr0
	lrwxrwxrwx  1 root  root           3 Oct  4 18:24 cdrw -> sr0
	drwxr-xr-x  2 root  root        3.6K Oct  4 18:24 char
	crw-------  1 root  root      5,   1 Oct  4 18:24 console
	lrwxrwxrwx  1 root  root          11 Oct  4 18:24 core -> /proc/kcore
	drwxr-xr-x  2 root  root          60 Oct  4 18:24 cpu
	crw-------  1 root  root     10,  59 Oct  4 18:24 cpu_dma_latency
	crw-------  1 root  root     10, 203 Oct  4 18:24 cuse
	drwxr-xr-x  5 root  root         100 Oct  4 18:24 disk
	brw-rw----  1 root  disk    252,   0 Oct  4 18:24 dm-0
	brw-rw----  1 root  disk    252,   1 Oct  4 18:24 dm-1
  <1><2------><3> <4>    <5>    <6-----> <7---------> <8>    

    第一项：代表这个文件的类型与权限(permission):

        当为[ d ]则是目录；
        当为[ - ]则是文件；
        若是[ l ]则是链接；
        若是[ b ]则是设备；表示可供储存的接口设备(可随机存取装置)
        若是[ c ]则表串口的串行端口设备,例如键盘、鼠标,显示器等
	

    第二项：以三个为一组,且均为{rwx}的三个参数的组合,r=读取,w=写入,x=执行:

        当为[ r ]则是读取权限,此权限还可以用数字4代表.
        当为[ w ]则是写入权限,此权限还可以用数字2代表.
        若是[ x ]则是执行权限,此权限还可以用数字1代表.
		
		
	第三项：代表文件的链接数;(文件:硬链接数 目录:目录下的子目录个数).
	第四项：代表文件的文件的所有者(属主)
	第五项：代表文件的文件的所属组(属组)
	第六项：代表文件大小(默认单位字节byte)
	第七项：代表文件最后一次修改时间
	第八项：代表文件的文件名



.. end


文件默认权限
============

当我们默认使用普通cecgw用户创建一个文件夹时，cecgw用户和组拥有该文件夹的rwx权限，而其他用户只有读和执行权限. 即其他用户可以cd(cd命令需要可执行权限)进入该文件夹，并且可以读取文件夹下的目录信息。
当我们设置文件目录为770(其他用户读写执行全部禁止)时，ls命令将被禁止，其他用户没有读权限.
当我们设置文件目录为774(只读权限)时，cd命令将失效，但可以ls.
当我们设置文件目录为771(只执行权限)时，可以cd到目录，ls等读命令将被禁止。

.. code-block:: console

	cecgw@ubuntu:~/test$ ll -d directory/
	drwxrwxr-x 2 cecgw cecgw 4096 Oct  5 10:28 directory/

.. end

.. Note::

    1.当仅r权限作用在目录上的时候，表示用户可以短列出查看目录下的文件名。
    2.当仅w权限作用在目录上的时候，没有意义。
    3.当仅x权限作用在目录上的时候，表示用户可以进入且可以访问目录下的文件，但不能列出文件名。
    4.当仅rw权限同时作用在目录上的时候，权限等同于仅r权限作用在目录。
    5.当仅rx权限同时作用在目录上的时候，表示用户可以进入，可以访问子文件，同时可以列出文件。
    6.当仅wx权限同时作用在目录上的时候，表示用户可以进入，可以访问子文件，可以创建及删除，但不能列出。
    7.当rwx权限同时作用在目录上的时候，表示用户有完整权限。

当我们默认使用普通cecgw用户创建一个文件时，cecgw用户和组拥有该文件夹的rw权限，而其他用户只有读权限.

.. Note::

    1.当仅r权限作用在文件上的时候，表示用户可以读取该文件的内容。
    2.当仅w权限作用在文件上的时候，表示用户可以修改该文件的内容。
    3.当仅x权限作用在文件上的时候，没有意义。
    4.当rw同时作用在文件上的时候，表示用户可以读写文件。
    5.当rx同时作用在文件上的时候，表示用户可以读且可以执行该文件。
    6.当wx同时作用在文件上的时候，权限与仅w相同。
    7.当rwx同时作用在文件上的时候，用户可以读写执行。

上述文件夹及文件的属性是由系统默认掩码控制的,即umask。umask命令用来设置限制新建文件权限的掩码,当新文件被创建时,其最初的权限由文件创建掩码决定,用户每次注册进入系统时,umask命令都被执行,并自动设置掩码mode来限制新文件的权限,用户可以通过再次执行umask命令来改变默认值,新的权限将会把旧的覆盖掉.

.. code-block:: console

	root@ubuntu:/home/cecgw/test# umask # root umask=0022
	0022
	cecgw@ubuntu:~/test/directory$ umask -S # 普通用户umask=0002
	u=rwx,g=rwx,o=rx
	cecgw@ubuntu:~/test/directory$ umask
	0002
	# linux系统中，umask的值决定了创建目录及文件的初始权限.
	
.. end

文件ACL权限
===========

ACL(Access Control List)，访问控制列表。为何linux操作系统中需要ACL？他与文件权限之间是什么关系？实际上，ACL提供了一种更为细粒度的文件访问控制机制。
在文件权限中，可以设置文件被访问的用户及用户组以及其他用户的权限。这种粒度显然比较粗糙，假设我们要设置其他用户中的一位具有特殊权限，那么使用文件默认权限显然解决不了该问题，那么ACL即可派上用场（other 用户太大，我们需要更细粒度的访问控制机制，use ACL）。
ACL可以针对单一用户、单一文件或目录来进行r、w、x的权限设置，对于需要特殊权限的使用状况非常有帮助.

查看系统是否支持ACL
+++++++++++++++++++

我们看某个文件（Linux系统中目录也是文件，一切皆是文件）是否支持 ACL 权限，首先要看文件所在的分区是否支持 ACL 权限。

.. code-block:: console

	root@ubuntu:/home/cecgw/test/directory# df -h
	Filesystem                   Size  Used Avail Use% Mounted on
	udev                         2.0G     0  2.0G   0% /dev
	tmpfs                        394M   15M  379M   4% /run
	/dev/mapper/ubuntu--vg-root  112G   21G   86G  20% /
	tmpfs                        2.0G     0  2.0G   0% /dev/shm
	tmpfs                        5.0M     0  5.0M   0% /run/lock
	tmpfs                        2.0G     0  2.0G   0% /sys/fs/cgroup
	/dev/sda1                    472M   57M  391M  13% /boot
	cgmfs                        100K     0  100K   0% /run/cgmanager/fs
	tmpfs                        100K     0  100K   0% /run/lxcfs/controllers
	tmpfs                        394M     0  394M   0% /run/user/0
	tmpfs                        394M     0  394M   0% /run/user/1000
	root@ubuntu:/home/cecgw/test/directory# dumpe2fs -h /dev/mapper/ubuntu--vg-root # xfs文件类型使用xfs_info /dev/xx
	dumpe2fs 1.42.13 (17-May-2015)
	Filesystem volume name:   <none>
	Last mounted on:          /
	Filesystem UUID:          97a753f8-1482-445a-a877-f0e2565f8dde
	Filesystem magic number:  0xEF53
	Filesystem revision #:    1 (dynamic)
	Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent flex_bg sparse_super large_file huge_file uninit_bg dir_nlink extra_isize
	Filesystem flags:         signed_directory_hash 
	Default mount options:    user_xattr acl  #支持
	
.. end

如果系统默认无acl的支持,那你可以这样做,来手动开启ACL：在/etc/fstab文件中options一项中，写入acl即可。

.. code-block:: console

	cecgw@ubuntu:~/test/directory$ ll -d acl/ # 创建acl文件夹
	drwxrwxr-x 2 cecgw cecgw 4096 Oct  5 11:39 acl//
	
	cecgw@ubuntu:~/test/directory$ getfacl acl #查看acl文件夹默认acl
	# file: acl
	# owner: cecgw
	# group: cecgw
	user::rwx
	group::rwx
	other::r-x
	
	# 此时我们使用hadoop用户无法在该文件夹下创建hadoop文件.
	hadoop@ubuntu:/home/cecgw/test/directory/acl$ touch hadoop
	touch: cannot touch 'hadoop': Permission denied
	# 使用acl命令增加hadoop对acl使用权限.
	cecgw@ubuntu:~/test/directory$ setfacl -m u:hadoop:rwx acl/
	cecgw@ubuntu:~/test/directory$ ll -d acl
	drwxrwxr-x+ 2 cecgw cecgw 4096 Oct  5 11:39 acl/ # note：du对other用户仍然是读和执行权限，但文件权限信息一列多了一个“+”号。
	# 查看文件acl 信息.
	cecgw@ubuntu:~/test/directory$ getfacl acl/
	# file: acl/
	# owner: cecgw
	# group: cecgw
	user::rwx
	user:hadoop:rwx  #增加了对hadoop用户的读写权限.
	group::rwx
	mask::rwx
	other::r-x
    # 再次使用hadoop用户在该目录下创建文件.
	hadoop@ubuntu:/home/cecgw/test/directory/acl$ touch iamhadoop # 创建成功
	hadoop@ubuntu:/home/cecgw/test/directory/acl$ ls
	iamhadoop
	hadoop@ubuntu:/home/cecgw/test/directory/acl$ ll
	total 8
	drwxrwxr-x+ 2 cecgw  cecgw  4096 Oct  5 11:46 ./
	drwxrwxr-x  3 cecgw  cecgw  4096 Oct  5 11:39 ../
	-rw-rw-r--  1 hadoop hadoop    0 Oct  5 11:46 iamhadoop
	hadoop-1@ubuntu:/home/cecgw/test/directory/acl$ touch imahadoop-1 # 其他用户仍不可创建.
	touch: cannot touch 'imahadoop-1': Permission denied
	
	# 取消ACL
	cecgw@ubuntu:~/test/directory$ setfacl -x u:hadoop acl/
	cecgw@ubuntu:~/test/directory$ getfacl acl/
	# file: acl/
	# owner: cecgw
	# group: cecgw
	user::rwx
	group::rwx
	mask::rwx
	other::r-x
	#清空所有acl配置命令为 # setacl -b acl 
	#递归设置ACL权限：实现对目录上的ACL权限的递归设置(目录) setfacl -R -m u:hadoop:rwx acl/
	
.. end


sudo权限
========


sudo是linux系统管理指令,是允许系统管理员让普通用户执行一些或者全部的root命令的一个工具,如halt,reboot,su等,这样不仅减少了root用户的登录,和管理时间,同样也提高了安全性,sudo不是对shell的一个代替,它是面向每个命令的.

sudo命令用来以其他身份来执行命令,预设的身份为root,在/etc/sudoers中设置了可执行sudo指令的用户.
为什么会出现sudo授权?首先在Linux的管理中,不是每一个系统管理员都能用root来管理服务器，平时使用普通用户管理操作系统,降低误操作几率,需要时su切换到root.su用户身份的切换,普通管理员要使用root的权限必须知道root的密码,这样会造成很多普通用户需要知道root的密码,导致系统的安全性降低.sudo可以为普通用户赋予一定权限,使普通用户能使用一些root才能执行的命令和操作,来完成正常的服务器管理工作,这种按需分配权限的方式可以让我们工作在一个相对安全的环境下,并且普通用户不需要知道root的密码.

sudo 工作步骤：
a.通过visudo命令或者vim /etc/sudoers 来对某用户授权
b.当普通用户执行/sbin或/usr/sbin下的命令时,系统会查询/etc/sudoers文件中是否有root对其的授权
c.当查询普通用户已拥有授权后,需要输入普通用户的密码来确认用户身份
d.若密码输入成功后,则执行对应已经授权的命令

openstack中频繁使用该机制.

实例1：给cecgw用户授予root的所有命令.

.. code-block:: console

	## The COMMANDS section may have other options added to it.
	##
	## Allow root to run any commands anywhere 
	root    ALL=(ALL)       ALL     
	cecgw   ALL=(ALL)       ALL   #写入以下参数

.. end


实例2：授权cecgw用户只可以重启服务器

.. code-block:: console

	vim /etc/sudoers

	## The COMMANDS section may have other options added to it.
	##
	## Allow root to run any commands anywhere
	root    ALL=(ALL)       ALL
	cecgw  ALL=(ALL)       /sbin/shutdown -r now   #写入以下参数

.. end




文件的特殊权限SetUid & SetGid & Sticky BIT
==========================================

https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits

SetUID
++++++

When the setuid bit is used, the behavior described above it's modified so that when an executable is launched, it does not run with the privileges of the user who launched it, but with that of the file owner instead. So, for example, if an executable has the setuid bit set on it, and it's owned by root, when launched by a normal user, it will run with root privileges. It should be clear why this represents a potential security risk, if not used correctly. 

当一个具有执行权限的文件设置SetUID权限后,用户执行这个文件时,将以文件所有者的身份执行.

为了更加清楚的了解这个特性,我们打个比方,普通用户根本没有修改/etc/passwd和/etc/shadow的权利,但是它还是可以通过passwd命令修改自己的密码,这就与SUID这个权限有关.

.. code-block:: console

	root@ubuntu:/home/cecgw/test# ll /usr/bin/passwd
	-rwsr-xr-x 1 root root 54256 May 17  2017 /usr/bin/passwd*

.. end

.. Note::

SetUID 权限的特点:
1.只有可以执行的二进制程序才能设定SUID权限
2.命令执行者要对该程序拥有x（执行）权限
3.命令执行者在执行该程序时获得该程序文件属主的身份（灵魂附体）
4.SetUID权限只在该程序执行过程中有效，也就是说身份改变只在程序执行过程中有效

设置命令：

.. code-block:: console
 
    chmod u+s test   || chmod 4755 test
	cecgw@ubuntu:~/test/directory$ chmod 4755 setuid 
	cecgw@ubuntu:~/test/directory$ ls
	acl  setuid
	cecgw@ubuntu:~/test/directory$ 
	cecgw@ubuntu:~/test/directory$ 
	cecgw@ubuntu:~/test/directory$ ll
	total 12
	drwxrwxr-x  3 cecgw cecgw 4096 Oct  5 12:06 ./
	drwxrwxr-x  5 cecgw cecgw 4096 Oct  5 11:39 ../
	drwxrwxr-x+ 2 cecgw cecgw 4096 Oct  5 11:46 acl/
	-rwsr-xr-x  1 cecgw cecgw    0 Oct  5 12:06 setuid*  #x变成s,如果是S，则说明文件不具备x权限，不生效.

.. end



SETGID
++++++

Unlike the setuid bit, the setgid bit has effect on both files and directories. In the first case, the file which has the setgid bit set, when executed, instead of running with the privileges of the group of the user who started it, runs with those of the group which owns the file: in other words, the group ID of the process will be the same of that of the file.

When used on a directory, instead, the setgid bit alters the standard behavior so that the group of the files created inside said directory, will not be that of the user who created them, but that of the parent directory itself. This is often used to ease the `sharing of files` (files will be modifiable by all the users that are part of said group). Just like the setuid, the setgid bit can easily be spotted (in this case on a test directory):

.. Note::

SetGID 针对文件的作用:
1.只有可执行的二进制程序才能设置SGID权限
2.命令执行者要对该程序拥有x（执行）权限
3.命令执行在执行程序的时候，组身份升级为该程序文件的属组
4.SetGID权限同样只在该程序执行过程中有效，也就是说组身份改变只在程序执行过程中有效
5.执行该文件时，文件的有效gid为该文件的组所有者.

.. Note::

SetGID 针对目录的作用:
1.普通用户必须对此目录拥有r和x权限，才能进入此目录
2.普通用户在此目录中的有效组会变成此目录的属组
3.普通用户对此目录拥有w权限时，新建的文件的默认属组是这个目录的属组

.. code-block:: console

	cecgw@ubuntu:~/test$ ll -d gid/ # 设置gid
	drwxrwsrwx 2 cecgw cecgw 4096 Oct  5 14:41 gid//
	hadoop@ubuntu:/home/cecgw/test/gid$ mkdir hadoop
	hadoop@ubuntu:/home/cecgw/test/gid$ ll -d hadoop/
	drwxrwsr-x 2 hadoop cecgw 4096 Oct  5 14:42 hadoop// # 使用hadoop创建hadoop目录，所属组变为cecgw.
	cecgw@ubuntu:~/test/gid$ cd hadoop/
	cecgw@ubuntu:~/test/gid/hadoop$ ls
	cecgw@ubuntu:~/test/gid/hadoop$ touch cecgw
	cecgw@ubuntu:~/test/gid/hadoop$ ll
	total 8
	drwxrwsr-x 2 hadoop cecgw 4096 Oct  5 14:46 ./
	drwxrwsrwx 3 cecgw  cecgw 4096 Oct  5 14:42 ../
	-rw-rw-r-- 1 cecgw  cecgw    0 Oct  5 14:46 cecgw #在设置gid的目录下使用cecgw可以在hadoop目录下创建文件.
	>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
	hadoop@ubuntu:/home/cecgw/test/directory$ mkdir hadoop # 正常状态下如果不设置gid，同时开放directory所有权限，创建的文件属性均为创建者信息.
	hadoop@ubuntu:/home/cecgw/test/directory$ ll
	total 12
	drwxrwxrwx 3 cecgw  cecgw  4096 Oct  5 14:44 ./
	drwxrwxr-x 5 cecgw  cecgw  4096 Oct  5 14:41 ../
	drwxrwxr-x 2 hadoop hadoop 4096 Oct  5 14:44 hadoop/
	cecgw@ubuntu:~/test/directory/hadoop$ touch cecgw
	touch: cannot touch 'cecgw': Permission denied # 在该目录下创建文件不允许.

.. end

Sticky BIT
++++++++++

The sticky bit works in a different way: while it has no effect on files, when used on a directory, all the files in said directory will be modifiable only by their owners. A typical case in which it is used, involves the /tmp directory. Typically this directory is writable by all users on the system, so to make impossible for one user to delete the files of another one, the sticky bit is set: 

.. Note::

SetBID：黏着位只能对目录有效,在设置了黏着位的目录下,只能删除自己创建的文件,其他人创建的文件只能浏览,root除外.
系统用例：/tmp目录。
1.粘着位目前只对目录有效
2.普通用户对该目录拥有w和x权限,即普通用户可以在此目录拥有写入权限
3.赋予了粘着位后除了root可以删除所有文件,普通用户就算拥有w权限,也只能删除自己建立的文件,但是不能删除其他用户建立的文件


示例：

.. code-block:: console

	root@ubuntu:/# ll -d /tmp/
	drwxrwxrwt 10 root root 4096 Oct  5 13:17 /tmp// # tmp 目录对所有的用户均有读写权限.
	cecgw@ubuntu:/tmp$ mkdir cecgw # 使用cecgw用户创建/tmp/cecgw 目录. 
	cecgw@ubuntu:/tmp$ ls
	cecgw  hsperfdata_root  systemd-private-6c37aba661634e3095b58286f0b6086c-systemd-timesyncd.service-0K3uju  vmware-root
	cecgw@ubuntu:/tmp$ ll
	total 44
	drwxrwxrwt 11 root  root  4096 Oct  5 13:50 ./
	drwxr-xr-x 23 root  root  4096 Jun 20 14:43 ../
	drwxrwxr-x  2 cecgw cecgw 4096 Oct  5 13:50 cecgw/ #在该目录下创建文件。

	cecgw@ubuntu:/tmp$ chmod 777 cecgw/ # 赋予cecgw所有权限。
	hadoop@ubuntu:/tmp$ rm -rf cecgw/ # 使用hadoop删除,被禁止。即使hadoop拥有rwx权限.
	rm: cannot remove 'cecgw/': Operation not permitted
	#########如果非黏着位文件夹，则可以正常删除.
	root@ubuntu:/home/cecgw# mkdir /abc # 创建abc
	root@ubuntu:/home/cecgw# chmod 777 /abc/  #赋予other所有权限
	root@ubuntu:/home/cecgw# su cecgw
	cecgw@ubuntu:~$ cd /abc/
	cecgw@ubuntu:/abc$ touch cecgw # 使用cecgw用户创建 cecgw文件.
	cecgw@ubuntu:/abc$ ll
	-rw-rw-r--  1 cecgw cecgw    0 Oct  5 14:20 cecgw
	cecgw@ubuntu:/abc$ su hadoop
	Password: 
	hadoop@ubuntu:/abc$ ls
	cecgw
	hadoop@ubuntu:/abc$ rm cecgw -rf # 使用hadoop用户可以删除cecgw文件.
	###
	我们给abc赋予粘滞位。
	cecgw@ubuntu:/abc$ ll -d /abc/
	drwxrwxrwt 2 root root 4096 Oct  5 14:27 /abc//
	使用cecgw 创建cecgw文件.
	cecgw@ubuntu:/abc$ touch cecgw
	cecgw@ubuntu:/abc$ ls
	cecgw
	cecgw@ubuntu:/abc$ ll
	total 8
	drwxrwxrwt  2 root  root  4096 Oct  5 14:28 ./
	drwxr-xr-x 24 root  root  4096 Oct  5 14:20 ../
	-rw-rw-r--  1 cecgw cecgw    0 Oct  5 14:28 cecgw
	再次使用hadoop删除看下是否能够删除该文件.
	hadoop@ubuntu:/abc$ rm -rf cecgw 
	rm: cannot remove 'cecgw': Operation not permitted # 提示不能删除，粘滞键生效.

.. end

chattr权限
==========

文件的隐藏属性有,chattr/lsattr,这些隐藏的属性确实对于系统有很大的帮助的,尤其是在系统安全(Security)上面,不过要先强调的是,chattr命令只能在Ext3/Ext4的文件系统上面生效,其他的文件系统可能就无法支持这个命令.


.. Note::

chattr --help
命令语法：[ chattr [+-=] [状态] [选项] 文件或目录 ]
        p              #输出的权限掩码可直接作为指令来执行
        A              #锁定(atime)访问时间
        S              #异步写入磁盘(sync)
        a              #设置只能添加数据,不能删除和修改
        c              #自动将此文件压缩,需要时自动解压缩
        d              #在dump备份时,不会备份此文件
        i              #将文件设置为只读
        s              #文件被删除将永久移除
        u              #文件删除不永久移除
        -R             #递归处理,连同子目录一起生效
        -V             #显示指令执行过程

示例一：只能增加不能删除.

.. code-block:: console

	root@ubuntu:/home/cecgw/test# chattr +a chattr/ # 只能使用root用户，该命令意思为chattr目录中的文件只能增加不能删除.
	root@ubuntu:/home/cecgw/test# cd chattr/
	root@ubuntu:/home/cecgw/test/chattr# ls
	root@ubuntu:/home/cecgw/test/chattr# touch addfile
	root@ubuntu:/home/cecgw/test/chattr# ls
	addfile
	root@ubuntu:/home/cecgw/test/chattr# rm -rf addfile 
	rm: cannot remove 'addfile': Operation not permitted # 删除被拒绝
	

.. end

示例二：检查隐藏属性.

.. code-block:: console

	root@ubuntu:/home/cecgw/test# lsattr -a ./chattr/
	-------------e-- ./chattr/..
	-----a-------e-- ./chattr/.
	-------------e-- ./chattr/addfile

.. end

示例三：设置某个文件不能被修改.

.. code-block:: console

	root@ubuntu:/home/cecgw/test/chattr# chattr +i addfile 
	root@ubuntu:/home/cecgw/test/chattr# vi addfile 
	root@ubuntu:/home/cecgw/test/chattr# lsattr -a addfile
	----i--------e-- addfile

.. end

进程权限
========
如何更好的理解进程和进程权限？通常把进程定义为程序执行时的一个实例。通俗的理解，是进程利用计算机资源帮助我们执行各种计算任务。也可以说，进程是我们用于访问计算机资源的代理。从这个角度理解，进程这个代理人理应具备“主人”的权限，同时操作系统也应该根据“主人”的权限，限制代理人对资源的访问，也就是说进程必须携带发起这个进程的用户的身份信息才能够进行合法的操作。

.. Note::
注意与线程的区别，对于操作系统来说，一个任务就是一个进程（Process），比如打开一个浏览器就是启动一个浏览器进程，打开一个记事本就启动了一个记事本进程，打开两个记事本就启动了两个记事本进程，打开一个Word就启动了一个Word进程。有些进程还不止同时干一件事，比如Word，它可以同时进行打字、拼写检查、打印等事情。在一个进程内部，要同时干多件事，就需要同时运行多个“子任务”，我们把进程内的这些“子任务”称为线程（Thread）。一个进程至少有一个线程。线程是最小的执行单元，而进程由至少一个线程组成。

进程权限与用户管理
++++++++++++++++++

.. code-block:: console

	1、我们可以通过ps -aux 查看进程相关信息。
	root@ubuntu:/home/cecgw# ps -aux|grep find
	cecgw     15447  0.2  0.0  19356  3440 pts/0    T    20:25   0:07 find / -inum 4196151
	# 可以看到，15447进程是以cecgw用户运行。
	2、通过vi /etc/passwd 可以查看到cecgw uid 及gid分别为1000
	cecgw:x:1000:1000:cecgw,,,:/home/cecgw:/bin/bash
	3、通过 vi /proc/<PID>/status 命令，我们可以查看到进程所属的用户和组相关的信息：
	示例一：
	root@ubuntu:/home/cecgw# cat /proc/15447/status 
	Name:	find
	State:	T (stopped)
	Tgid:	15447
	Ngid:	0
	Pid:	15447
	PPid:	15194
	TracerPid:	0
	Uid:	1000	1000	1000	1000
	Gid:	1000	1000	1000	1000
	.....
	我们可以观察到一个进程的状态信息中包括了uid，gid的信息位。有4个信息位，分别为 real user id, effective user id, saved set user id 和 filesystem UID，第二行则是对应的组 ID。
	示例二：(real user id != effective user id)
	cecgw@ubuntu:~$ passwd
	Changing password for cecgw.
	(current) UNIX password: 
	cecgw@ubuntu:~$ ps -aux|grep passwd  
	root      16064  0.0  0.0  42724  3176 pts/1    S+   21:31   0:00 passwd 
	# 注意虽然是以cecgw用户启动passwd进行，但仍然使用root执行.
	root@ubuntu:/home/cecgw# cat /proc/15970/status 
	Name:	passwd
	State:	S (sleeping)
	Tgid:	15970
	Ngid:	0
	Pid:	15970
	PPid:	15960
	TracerPid:	0
	Uid:	1000	0	0	0   # 注意：read user id = cecgw， effective user id = root 
	Gid:	1000	1000	1000	1000
	

.. end 


.. Note::

	1、Real user id <p>
	real user id 是执行进程者的 user id，一般情况下就是用户登录时的 user id。子进程的 real user id 从父进继承。通常这个是不更改的，也不需要更改。比如我以用户 cecgw 登录 Linux 系统，我接下来运行的所有命令的进程的 real user id 都是 cecgw 的 user id。
	2、Effective user id
	如果要判断一个进程是否对某个文件有操作权限，验证的是进程的 effective user id，而不是 real user id。
	通常我们是不建议直接使用 root 用户进行操作的，但是在很多情况下，程序可能需要特殊的权限。比如 passwd 程序需要 root 权限才能够为普通用户修改密码，一些 services 程序的操作也经常需要特殊的权限。为此，Linux 中设计了一些特殊的权限，如SUID,SGID,SBIT。以 passwd 程序为例(如上述示例二)，为二进制可执行文件 /usr/bin/passwd 设置  set-user-id bit=ON，这个可执行文件被用 exec 启动之后的进程的 effective user id 就是这个可执行文件的 owner id，而并非父进程的 real user id。如果 set-user-id bit=OFF 的时候，这个被 exec 起来的进程的 effective user id 应该是等于进程的 user id 的。所以，effective user id 存在的意义在于，它可能和 real user id 不同。
	3、saved set user id
	saved set user id 相当于是一个 buffer，在 exec 函数启动之后，它会拷贝 effective user id 位的信息覆盖自己。对于非 root 用户来说，可以在未来使用 setuid() 函数将 effective user id 设置成为 real user id 或 saved set user id 中的任何一个。但是不允许非 root 用户用 setuid() 函数把 effective user id 设置成为任何第三个 user id。
	对于 root 用户来说，调用 setuid() 的时候，将会设置所有的这三个 user id。
	4、filesystem UID 	
	

Linux 系统通过进程的有效用户 ID(effective user id) 和有效用户组 ID(effective group id) 来决定进程对系统资源的访问权限。通过 ps aux 查看的结果中，第一列显示的就是进程的 effective user.






.. Note::
   
   For KAFKA。
