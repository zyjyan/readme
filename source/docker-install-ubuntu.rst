Ubuntu 安装Docker
~~~~~~~~~~~~~~~~~~~~~

前面已经介绍了Docker对各个操作系统以及平台的支持。本文中，将选择ubuntu14.04(AMD64)操作系统作为例，进行Docker CE版本学习。

操作系统准备
---------------
ARM64，Ubuntu14.04LTS

卸载之前的Docker安装
--------------------
如果已经安装了Docker|docker-engine的，可以使用如下指令，完成旧版本的卸载：

.. code-block:: console

   $ sudo apt-get remove docker docker-engine docker.io

.. end
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

		 

.. figure:: ./image/docker/docker.png
   :width: 80%
   :align: center
   :alt: Message substitution diagram

end-21

   
