horizon+keystone+ceilometer
~~~~~~~~~~~~~~~~~~~~~~~~~~~

为何选择3个框架
----------------

主要原因如下：

.. code-block:: console

 # 懒
 # 熟悉
 # 还不错
 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
 一个值得参考的框架。
 设计较为全面。
 一个带认证的框架。
 一个带分布式交互的框架。
 一个优秀团队撰写的框架。
 一个用框架较多的框架。
 一个抽象思维较好的框架。
 总之，也没什么坏处。
 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
 
.. end


如何构建及验证
--------------

环境准备：ubuntu 18.04 server
依赖开源：openstack


.. code-block:: console

 root@ubuntu:/home/ubuntu# apt-get update # 更新源
 root@ubuntu:/etc/apt# add-apt-repository cloud-archive:rocky # 增加openstack 社区源
 root@ubuntu: apt-get update && apt-get dist-upgrade  # 更新 等待时间较长
 root@ubuntu: apt install python-openstackclient  # 按装openstack 客户端(sdk)
 root@ubuntu:/etc/apt# apt install mariadb-server python-pymysql # 安装mysql
 root@ubuntu:/etc/apt# touch /etc/mysql/mariadb.conf.d/99-openstack.cnf # 创建mysql openstack 配置文件
 root@ubuntu:/etc/apt# vi /etc/mysql/mariadb.conf.d/99-openstack.cnf 
  [mysqld]
  bind-address = 0.0.0.0

  default-storage-engine = innodb
  innodb_file_per_table = on
  max_connections = 4096
  collation-server = utf8_general_ci
  character-set-server = utf8
  # add the following info
 root@ubuntu:/etc/apt# service mysql restart# 重启Mysql

 root@ubuntu:/etc/apt# mysql_secure_installation
 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
 NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

 In order to log into MariaDB to secure it, we'll need the current
 password for the root user.  If you've just installed MariaDB, and
 you haven't set the root password yet, the password will be blank,
 so you should just press enter here.

 Enter current password for root (enter for none): 
	OK, successfully used password, moving on...

	Setting the root password ensures that nobody can log into the MariaDB
	root user without the proper authorisation.

	Set root password? [Y/n] y
	New password: 
	Re-enter new password: 
	Password updated successfully!
	Reloading privilege tables..
	 ... Success!


	By default, a MariaDB installation has an anonymous user, allowing anyone
	to log into MariaDB without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] n
	 ... skipping.

	By default, MariaDB comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	 Cleaning up...

	 All done!  If you've completed all of the above steps, your MariaDB
	 installation should now be secure.

	 Thanks for using MariaDB!
    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.

 root@ubuntu:/home/ubuntu# apt install rabbitmq-server # 安装rabitmq-server. 分布式消息队列 单机版本.
 root@ubuntu:/home/ubuntu# rabbitmqctl add_user openstack 1qaz2wsx # add openstack user 供消息队列用户认证使用.
 Creating user "openstack"
 root@ubuntu:/home/ubuntu# rabbitmqctl set_permissions openstack ".*" ".*" ".*" #使openstack 用户具有接入根权限.
 Setting permissions for user "openstack" in vhost "/"
 root@ubuntu:/home/ubuntu# apt install memcached python-memcache # 安装memcached 用于缓存数据使用,比如keystone token.提高访问速度.
 root@ubuntu:/home/ubuntu# service memcached restart
 
 >>>>>>>>>>> keystone install >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.
 root@ubuntu:/home/ubuntu# mysql # 创建keystone 数据库并赋予用户权限.
	Welcome to the MariaDB monitor.  Commands end with ; or \g.
	Your MariaDB connection id is 30
	Server version: 10.1.43-MariaDB-0ubuntu0.18.04.1 Ubuntu 18.04

	Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	MariaDB [(none)]> CREATE DATABASE keystone;
	MariaDB [keystone]>  GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
	    -> IDENTIFIED BY '1qaz2wsx';
	Query OK, 0 rows affected (0.00 sec)

	MariaDB [keystone]>  GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
	    -> IDENTIFIED BY '1qaz2wsx';
	Query OK, 0 rows affected (0.00 sec)
	MariaDB [mysql]> use mysql
        MariaDB [mysql]> UPDATE user SET plugin='mysql_native_password' WHERE user='keystone';
        MariaDB [mysql]> FLUSH PRIVILEGES;

        MariaDB [keystone]> quit
	Bye
 root@ubuntu:/home/ubuntu# apt install keystone  apache2 libapache2-mod-wsgi # 安装keystone需要的软件包
 root@ubuntu:/home/ubuntu# vi /etc/keystone/keystone.conf  # 做一个最基本的配置.
 root@ubuntu:/home/ubuntu# grep -vE  '^#|^$' /etc/keystone/keystone.conf  # 做最基本配置.keystone 配置项众多,值得研究.
	[DEFAULT]
	log_dir = /var/log/keystone
	[application_credential]
	[assignment]
	[auth]
	[cache]
	[catalog]
	[cors]
	[credential]
	[database]
	connection = mysql+pymysql://keystone:1qaz2wsx@localhost/keystone # 配置链接数据库认证方式.
	[domain_config]
	[endpoint_filter]
	[endpoint_policy]
	[eventlet_server]
	[extra_headers]
	Distribution = Ubuntu
	[federation]
	[fernet_tokens]
	[healthcheck]
	[identity]
	[identity_mapping]
	[ldap]
	[matchmaker_redis]
	[memcache]
	[oauth1]
	[oslo_messaging_amqp]
	[oslo_messaging_kafka]
	[oslo_messaging_notifications]
	[oslo_messaging_rabbit]
	[oslo_messaging_zmq]
	[oslo_middleware]
 root@ubuntu:/home/ubuntu# su -s /bin/sh -c "keystone-manage db_sync" keystone #生成数据库表.
	[oslo_policy]
	[policy]
	[profiler]
	[resource]
	[revoke]
	[role]
	[saml]
	[security_compliance]
	[shadow_users]
	[signing]
	[token]
	provider = fernet # 配置最基本的token认证方式,我们这里选择uuid. 目前keystone 支持4类认证.包括 uuid,fernet,pki,pkiz供用户选择.感兴趣的可以分析清楚. 之前的版本均采用uuid,有一点问题，后来版本均采用fernet.
	[tokenless_auth]
	[trust]
	[unified_limit]
	[wsgi]

 root@ubuntu:/home/ubuntu# su -s /bin/sh -c "keystone-manage db_sync" keystone
 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.
 tail -f /var/log/keystone/keystone-manage.log

	2020-01-13 18:01:18.543 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:18.543 7848 INFO migrate.versioning.api [-] 50 -> 51... 
	2020-01-13 18:01:18.552 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:18.552 7848 INFO migrate.versioning.api [-] 51 -> 52... 
	2020-01-13 18:01:18.561 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:18.587 7848 INFO migrate.versioning.api [-] 0 -> 1... 
	2020-01-13 18:01:18.591 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:18.591 7848 INFO migrate.versioning.api [-] 1 -> 2... 
	2020-01-13 18:01:18.853 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:18.854 7848 INFO migrate.versioning.api [-] 2 -> 3... 
	2020-01-13 18:01:19.211 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.211 7848 INFO migrate.versioning.api [-] 3 -> 4... 
	2020-01-13 18:01:19.571 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.572 7848 INFO migrate.versioning.api [-] 4 -> 5... 
	2020-01-13 18:01:19.581 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.582 7848 INFO migrate.versioning.api [-] 5 -> 6... 
	2020-01-13 18:01:19.589 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.589 7848 INFO migrate.versioning.api [-] 6 -> 7... 
	2020-01-13 18:01:19.597 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.598 7848 INFO migrate.versioning.api [-] 7 -> 8... 
	2020-01-13 18:01:19.607 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.607 7848 INFO migrate.versioning.api [-] 8 -> 9... 
	2020-01-13 18:01:19.616 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.617 7848 INFO migrate.versioning.api [-] 9 -> 10... 
	2020-01-13 18:01:19.625 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.625 7848 INFO migrate.versioning.api [-] 10 -> 11... 
	2020-01-13 18:01:19.712 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.714 7848 INFO migrate.versioning.api [-] 11 -> 12... 
	2020-01-13 18:01:19.837 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:19.839 7848 INFO migrate.versioning.api [-] 12 -> 13... 
	2020-01-13 18:01:20.111 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.113 7848 INFO migrate.versioning.api [-] 13 -> 14... 
	2020-01-13 18:01:20.643 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.644 7848 INFO migrate.versioning.api [-] 14 -> 15... 
	2020-01-13 18:01:20.801 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.804 7848 INFO migrate.versioning.api [-] 15 -> 16... 
	2020-01-13 18:01:20.822 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.824 7848 INFO migrate.versioning.api [-] 16 -> 17... 
	2020-01-13 18:01:20.839 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.840 7848 INFO migrate.versioning.api [-] 17 -> 18... 
	2020-01-13 18:01:20.846 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.847 7848 INFO migrate.versioning.api [-] 18 -> 19... 
	2020-01-13 18:01:20.855 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.856 7848 INFO migrate.versioning.api [-] 19 -> 20... 
	2020-01-13 18:01:20.864 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.865 7848 INFO migrate.versioning.api [-] 20 -> 21... 
	2020-01-13 18:01:20.873 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.874 7848 INFO migrate.versioning.api [-] 21 -> 22... 
	2020-01-13 18:01:20.883 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.884 7848 INFO migrate.versioning.api [-] 22 -> 23... 
	2020-01-13 18:01:20.891 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:20.892 7848 INFO migrate.versioning.api [-] 23 -> 24... 
	2020-01-13 18:01:21.016 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.017 7848 INFO migrate.versioning.api [-] 24 -> 25... 
	2020-01-13 18:01:21.027 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.028 7848 INFO migrate.versioning.api [-] 25 -> 26... 
	2020-01-13 18:01:21.039 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.040 7848 INFO migrate.versioning.api [-] 26 -> 27... 
	2020-01-13 18:01:21.051 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.052 7848 INFO migrate.versioning.api [-] 27 -> 28... 
	2020-01-13 18:01:21.062 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.063 7848 INFO migrate.versioning.api [-] 28 -> 29... 
	2020-01-13 18:01:21.073 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.075 7848 INFO migrate.versioning.api [-] 29 -> 30... 
	2020-01-13 18:01:21.083 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.084 7848 INFO migrate.versioning.api [-] 30 -> 31... 
	2020-01-13 18:01:21.092 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.092 7848 INFO migrate.versioning.api [-] 31 -> 32... 
	2020-01-13 18:01:21.121 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.121 7848 INFO migrate.versioning.api [-] 32 -> 33... 
	2020-01-13 18:01:21.130 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.130 7848 INFO migrate.versioning.api [-] 33 -> 34... 
	2020-01-13 18:01:21.142 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.143 7848 INFO migrate.versioning.api [-] 34 -> 35... 
	2020-01-13 18:01:21.270 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.272 7848 INFO migrate.versioning.api [-] 35 -> 36... 
	2020-01-13 18:01:21.400 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.402 7848 INFO migrate.versioning.api [-] 36 -> 37... 
	2020-01-13 18:01:21.441 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.441 7848 INFO migrate.versioning.api [-] 37 -> 38... 
	2020-01-13 18:01:21.450 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.450 7848 INFO migrate.versioning.api [-] 38 -> 39... 
	2020-01-13 18:01:21.459 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.460 7848 INFO migrate.versioning.api [-] 39 -> 40... 
	2020-01-13 18:01:21.468 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.469 7848 INFO migrate.versioning.api [-] 40 -> 41... 
	2020-01-13 18:01:21.477 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.477 7848 INFO migrate.versioning.api [-] 41 -> 42... 
	2020-01-13 18:01:21.486 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.487 7848 INFO migrate.versioning.api [-] 42 -> 43... 
	2020-01-13 18:01:21.495 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.496 7848 INFO migrate.versioning.api [-] 43 -> 44... 
	2020-01-13 18:01:21.504 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.504 7848 INFO migrate.versioning.api [-] 44 -> 45... 
	2020-01-13 18:01:21.513 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.514 7848 INFO migrate.versioning.api [-] 45 -> 46... 
	2020-01-13 18:01:21.522 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.523 7848 INFO migrate.versioning.api [-] 46 -> 47... 
	2020-01-13 18:01:21.635 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.636 7848 INFO migrate.versioning.api [-] 47 -> 48... 
	2020-01-13 18:01:21.646 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.646 7848 INFO migrate.versioning.api [-] 48 -> 49... 
	2020-01-13 18:01:21.654 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.654 7848 INFO migrate.versioning.api [-] 49 -> 50... 
	2020-01-13 18:01:21.663 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.664 7848 INFO migrate.versioning.api [-] 50 -> 51... 
	2020-01-13 18:01:21.672 7848 INFO migrate.versioning.api [-] done
	2020-01-13 18:01:21.672 7848 INFO migrate.versioning.api [-] 51 -> 52... 
	2020-01-13 18:01:21.681 7848 INFO migrate.versioning.api [-] done
 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>...
 root@ubuntu:/home/ubuntu# keystone-manage bootstrap --bootstrap-password 1qaz2wsx \
 >   --bootstrap-admin-url http://ubuntu:5000/v3/ \
 >   --bootstrap-internal-url http://ubuntu:5000/v3/ \
 >   --bootstrap-public-url http://ubuntu:5000/v3/ \
 >   --bootstrap-region-id RegionOne # 生成keytone endpoint
 MariaDB [keystone]> select * from endpoint;
 +----------------------------------+--------------------+-----------+----------------------------------+------------------------+-------+---------+-----------+
 | id                               | legacy_endpoint_id | interface | service_id                       | url                    | extra | enabled | region_id |
 +----------------------------------+--------------------+-----------+----------------------------------+------------------------+-------+---------+-----------+
 | 07f8a36af7194a9894a212d4729f383f | NULL               | internal  | 5d2199e6a4f643738f0a72f74da7f1fb | http://ubuntu:5000/v3/ | {}    |       1 | RegionOne |
 | 93a8b806bc984b78addcc05ec6c2e014 | NULL               | admin     | 5d2199e6a4f643738f0a72f74da7f1fb | http://ubuntu:5000/v3/ | {}    |       1 | RegionOne |
 | b18dd1dc9b0148ca97a1136bfc8f922e | NULL               | public    | 5d2199e6a4f643738f0a72f74da7f1fb | http://ubuntu:5000/v3/ | {}    |       1 | RegionOne |
 +----------------------------------+--------------------+-----------+----------------------------------+------------------------+-------+---------+-----------+
 3 rows in set (0.00 sec)
 root@ubuntu:/etc/apache2# /etc/init.d/apache2 restart
 [ ok ] Restarting apache2 (via systemctl): apache2.service.
 root@ubuntu:/etc/apache2# touch /root/openstackrc
 root@ubuntu:/etc/apache2# vi /root/openstackrc 
 export OS_USERNAME=admin
 export OS_PASSWORD=1qaz2wsx
 export OS_PROJECT_NAME=admin
 export OS_USER_DOMAIN_NAME=Default
 export OS_PROJECT_DOMAIN_NAME=Default
 export OS_AUTH_URL=http://ubuntu:5000/v3
 export OS_IDENTITY_API_VERSION=3

 root@ubuntu:/etc/apache2# openstack domain create --description "An Example Domain" example #创建demain
 +-------------+----------------------------------+
 | Field       | Value                            |
 +-------------+----------------------------------+
 | description | An Example Domain                |
 | enabled     | True                             |
 | id          | 48edc71538d5434395a64889ff810042 |
 | name        | example                          |
 | tags        | []                               |
 +-------------+----------------------------------+ 

	root@ubuntu:/etc/apache2# openstack project create --domain default \
	>   --description "Service Project" service
	+-------------+----------------------------------+
	| Field       | Value                            |
	+-------------+----------------------------------+
	| description | Service Project                  |
	| domain_id   | default                          |
	| enabled     | True                             |
	| id          | d4774cd79b4b476d92cc2518888f10b0 |
	| is_domain   | False                            |
	| name        | service                          |
	| parent_id   | default                          |
	| tags        | []                               |
	+-------------+----------------------------------+
	root@ubuntu:/etc/apache2# openstack project create --domain default \
	>   --description "Demo Project" fying
	+-------------+----------------------------------+
	| Field       | Value                            |
	+-------------+----------------------------------+
	| description | Demo Project                     |
	| domain_id   | default                          |
	| enabled     | True                             |
	| id          | 14d0668d8d5c4ed1b82a733ef8e446bf |
	| is_domain   | False                            |
	| name        | fying                            |
	| parent_id   | default                          |
	| tags        | []                               |
	+-------------+----------------------------------+
	root@ubuntu:/etc/apache2# openstack user create --domain default \
	>   --password-prompt fying
	User Password:
	Repeat User Password:
	+---------------------+----------------------------------+
	| Field               | Value                            |
	+---------------------+----------------------------------+
	| domain_id           | default                          |
	| enabled             | True                             |
	| id                  | c5607a5305fb4e73acf6376b77baa855 |
	| name                | fying                            |
	| options             | {}                               |
	| password_expires_at | None                             |
	+---------------------+----------------------------------+
	root@ubuntu:/etc/apache2# openstack role create admin
	Conflict occurred attempting to store role - Duplicate entry found with name admin. (HTTP 409) (Request-ID: req-49569d88-0b4b-48ba-b88e-f3ce0dcc2f01)
	root@ubuntu:/etc/apache2# openstack role create user
	+-----------+----------------------------------+
	| Field     | Value                            |
	+-----------+----------------------------------+
	| domain_id | None                             |
	| id        | 7605d1d1b98b41f9a5d920b68acd4978 |
	| name      | user                             |
	+-----------+----------------------------------+
	root@ubuntu:/etc/apache2# openstack role add --project fying --user myuser user
	No user with a name or ID of 'myuser' exists.
	root@ubuntu:/etc/apache2# openstack role add --project fying --user fying user

 TEST 请求一个token.

	root@ubuntu:/etc/apache2#  openstack --os-auth-url http://ubuntu:5000/v3 \
	>   --os-project-domain-name Default --os-user-domain-name Default \
	>   --os-project-name admin --os-username admin token issue
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
	| Field      | Value                                                                                                                                                                                   |
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
	| expires    | 2020-01-13T11:47:45+0000                                                                                                                                                                |
	| id         | gAAAAABeHErRhnt9tZJc-5zV9ccM36TISGbFOSZ6VzY7ozWXL23RWP0TUwXhIi-qLgRYFYMUSvVKx3VGYae8QKL6LZ7nvE4h6zqznLnxsdSNazLKZzecSsus1u46pzb7YLpNpsmeZMP-J_1bjU7FDhLK97VQ5cPzBFIJZdao5WIGkE1eqmIBtK8 |
	| project_id | ee8ba2a1b05c4ea383bea1d1f8b0996d                                                                                                                                                        |
	| user_id    | 303212a7aea24e5194128f862a198667                                                                                                                                                        |
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

	root@ubuntu:/etc/apache2# openstack token issue
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
	| Field      | Value                                                                                                                                                                                   |
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
	| expires    | 2020-01-13T11:48:46+0000                                                                                                                                                                |
	| id         | gAAAAABeHEsOW2kzsK1bhAO1mRA4Ibjt1TpG5LKQArSg2wnMg-Z9Qf1KEozw5SlDVDkW_lfCQL9PpNNdjBfdKylBgtJvNlpvJmXNnVnQWbVJznwIbH1pIMNfmO5H2iV1UEJzQhpk7xocLN7vemF4PEfOa6T1wBeWIqsWLfOSSHQUT_VhuitVYzo |
	| project_id | ee8ba2a1b05c4ea383bea1d1f8b0996d                                                                                                                                                        |
	| user_id    | 303212a7aea24e5194128f862a198667                                                                                                                                                        |
	+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

	root@ubuntu:/etc/apache2# openstack service list # 列出服务信息,keystone,ceilometer,nova,nuetorn在keystone中都是一种服务，并且均有用户存在，同一由keystone service租户管理.
	+----------------------------------+----------+----------+
	| ID                               | Name     | Type     |
	+----------------------------------+----------+----------+
	| 5d2199e6a4f643738f0a72f74da7f1fb | keystone | identity |
	+----------------------------------+----------+----------+


	root@ubuntu:/etc/apache2# openstack user list # 列出该租户下的用户.
	+----------------------------------+-------+
	| ID                               | Name  |
	+----------------------------------+-------+
	| 303212a7aea24e5194128f862a198667 | admin |
	| c5607a5305fb4e73acf6376b77baa855 | fying |
	+----------------------------------+-------+

	root@ubuntu:/etc/apache2# openstack project list # 列出租户信息.
	+----------------------------------+---------+
	| ID                               | Name    |
	+----------------------------------+---------+
	| 14d0668d8d5c4ed1b82a733ef8e446bf | fying   |
	| d4774cd79b4b476d92cc2518888f10b0 | service |
	| ee8ba2a1b05c4ea383bea1d1f8b0996d | admin   |
	+----------------------------------+---------+

	root@ubuntu:/home/ubuntu# openstack endpoint list #列出服务入口,每一个组件（nova,keystone,ceilometer,neutron,cinder..）的对外发布入口都是以微服务组件的形式发布，提供统一的rest api，keystone负责统一api的管理及认证.这种微服务的形式，是可进行分布式或弹性部署的前提.
	+----------------------------------+-----------+--------------+--------------+---------+-----------+------------------------+
	| ID                               | Region    | Service Name | Service Type | Enabled | Interface | URL                    |
	+----------------------------------+-----------+--------------+--------------+---------+-----------+------------------------+
	| 07f8a36af7194a9894a212d4729f383f | RegionOne | keystone     | identity     | True    | internal  | http://ubuntu:5000/v3/ |
	| 93a8b806bc984b78addcc05ec6c2e014 | RegionOne | keystone     | identity     | True    | admin     | http://ubuntu:5000/v3/ |
	| b18dd1dc9b0148ca97a1136bfc8f922e | RegionOne | keystone     | identity     | True    | public    | http://ubuntu:5000/v3/ |
	+----------------------------------+-----------+--------------+--------------+---------+-----------+------------------------+
 >><<>><<>><<>><<>><<>><<>><<>><<>><<>><<>><<>><>><><><><><><<<>><<>><<><><><><><><><>><>><><>>>><><>>><><><>><<<<<<<<>>>>>><<<><><><><>>

 root@ubuntu:/home/ubuntu# apt-get install openstack-dashboard
 root@ubuntu:/usr/lib/python2.7/dist-packages/openstack_dashboard/local# vi local_settings.py
 >>>>>>>> set OPENSTACK_HOST = "ubuntu" >>>> ALLOWED_HOSTS = ['*'] >>> 
 >>CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': 'ubuntu:11211',
    },
 } >>>>
 >>OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST>>
 >>OPENSTACK_API_VERSIONS = {
    "identity": 3,
 }
 >>

 >> OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default">>
 >> TIME_ZONE = "UTC" >> 

 注意查看 /etc/apache2/conf-available/openstack-dashboard.conf 中的配置.这是apache link django 的配置. horizon是django的一个应用.
 如果涉及apache的配置文件有修改,记得执行 service apache2 reload 并重启apache2

 到这里，我们已经搭建了一个web框架，我们可以通过访问. http://ip/horizon 进行访问.


.. end

 到这里，我们已经搭建了一个web框架，我们可以通过访问. http://ip/horizon 进行访问.

.. figure:: image/fy-img/first_login.png
   :width: 80%
   :align: center
   :alt: first_login

我们使用命令行创建的默认用户登录. admin  pas**rd登录.

.. figure:: image/fy-img/first_login_in.png
   :width: 80%
   :align: center
   :alt: first_login_in

可以修改主题.

.. figure:: image/fy-img/first_login_in_1.png
   :width: 80%
   :align: center
   :alt: first_login_in_1

我们查看一下用户情况.

.. figure:: image/fy-img/first_login_think.png
   :width: 80%
   :align: center
   :alt:  first_login_think.png

查看下admin进行用户创建，能做什么操作。

.. figure:: image/fy-img/admin_create_user.png
   :width: 80%
   :align: center
   :alt:  admin_create_user.png

我们再以fying 登录查看下看.

.. figure:: image/fy-img/tenant_login.png
   :width: 80%
   :align: center
   :alt:  tenant_login.png

我们发现以fying登录，点击用户，发现只能看到自己的信息，并且不能进行用户创建的权限.↑

点击登录租户查看.

.. figure:: image/fy-img/tenant_tenant_user.png
   :width: 80%
   :align: center
   :alt:  tenant_tenant_user.png

我们发现也不能进行创建操作.因为fying只是一个普通用户. 连创建按钮查看的权力都没有. 事实上，程序并不会为某个用户创建独立的dashboard，但是却可
可以依据用户的角色，选择界面上哪些元素显示或者不显示. 这个是horizon的特色之一.也是代码设计者的巧妙之处.


停下来思考-1
------------

All right.

.. code-block:: console

随着openstack社区的更新，组件的安全已经变得越来越简单,并进行了很大的优化. 想起14年进行安装时，官方的文档还不全，按照官网操作基本一步一坑.
往往，人在踩坑的时候才会去思考. 

到目前为止，我们很顺利的搭建了:
一个基于django的web应用框架.
一个带认证的应用框架. 
一个使用keystone作为认证后端的框架.
一个基于rest的认证组件.
一个采用了多租户模型，多微服务应用管理，多角色管理的认证组件.
一个采用了严格api管控的认证组件.
一个采用模块化编程的前端框架。
一个基于restAPI进行交互的框架.
一个可分布式部署的框架.
一个可基于可控制dashboard-->pannel-->tab-->table-->button-->form灵活显示的前端框架.
一个...
我们仅仅搭建了horizon+keystone，却应该先停下来思考几个问题. 在相对简单的情况下想清楚一些问题，有助于在面临复杂问题的时候从容面对.
想明白了1+1. 1+2也不会太难. horizon是整个openstack项目面向用户的门户,构建了一个多租户模型应用,本身并不提供任何功能,所有的功能由 keystone、nova、neutron、cinder、glance、ceilometer等提供，从这个角度而言,keytone的地位与其余的项目一致,都作为horizon的能力供应服务. 但也有一些不同，keystone 不仅提供了hirizon的认证，也提供了 nova、neutron等其余组件对外暴漏API接口的权限认证,从这个角度讲，keystone提供了多个服务的统一认证框架,至于其余的组件，如何使用该认证,涉及的内容非常多，其中最主要的是paste框架,采用该框架,可以非常灵活的在其余组件的api供应组件中(ceimmeter-api、neutron-server、nova-api等)方便的加载keystone.

horizon是openstack中，从技术维度讲，是较为简单的一个，但确实业务逻辑构建最复杂的一个，需要有horizon完成基于nova,keystone,glance等组件的业
务流程，面向用户，提供云计算服务，所以对产品设计的人而言，非常值得借鉴.


不应该因为顺利停止思考:大概有这几个点需要进行深入的思考。
1、django如何部署在apache下？
2、我们刚才使用不同的用户为何看到的内容不一致？
3、我们刚才登录的两个用户角色范围是什么？他们如何定义？
4、openstack原生提供了什么样子的权限模型？
5、openstack原生提供的多租户模型全貌是什么？
6、刚才的一次登录发生了什么？
7、后台敲几个命令行，为何能够创建用户？
8、django如何采用keystone进行权限认证？
9、django如何连接数据库？
10、为何hirizon设置了dashboard-->pannel-->tab-->table-->form-->button等模板元素?
11、如何通过权限控制上述界面元素的显示?
12、一个用户可不可以拥有多个角色？
13、当一个用户拥有多个角色的时候，元素显示是或允许还是并允许？
14、一个用户可不可以在多个租户中？
15、admin 租户，service租户，tenant租户之间什么关系？
16、为何其余的组件没有安装，前端界面没有报错？并仅仅显示了已经安装的keystone相关功能？
17、创建用户发生了什么？
18、如何查看日志？
19、如何使用中文显示？
20、如何汉化？
21、horizon如何与keystone 交互.
22、keystone提供了token机制流程是怎么工作的？
23、keytone提供了多少种token认证机制？如何配置，如何选择？
24、keystone 如何实现多微服务管理？
25、keystone 如何通过policy实现对api的细粒度管理？
26、我想重构界面怎么做？
27、我想重构权限逻辑如何做？
28、我想重构租户模型怎么做？
29、为何有了password 还要有token认证的出现？
30、and so on.
31、再加上ceilometer呢？

.. end

>>>>>>>理解>>>>重构>>>>>>增值>>>>>合适>>>>>>


>>>>>Get what?>>>>> if..>>>>>>>>>>>>>>>>>>>>


django 如何部署在apache下
------------------------


.. code-block:: console

 python manage.py runserver 0.0.0.0:8000
 下正常运行起来了，那么接下来只需要配置apache2 即可
 1、安装所需要的软件包:

     apt-get install apache2
     apt-get install python-django
     apt-get install libapache2-mod-wsgi
 2、修改apache2配置文件，端口情况，这里的端口需要根据自己的需要指定 
      vi  /etc/apache2/ports.conf
      添加监听端口

.. end

.. figure:: image/fy-img/add_port_for_django.png
   :width: 80%
   :align: center
   :alt: add_port_for_django


.. code-block:: console

 3、添加配置项目，让apache可寻找 django入口
      touch /etc/apache2/sites-avilable/cloudsec.conf
      vi /etc/apche2/sites/avilable/cloudsec.conf
       <VirtualHost \*:81>
        ServerName 192.168.246.129 
       WSGIScriptAlias / /opt/cloud3.0/cloudsec/cloudsec/wsgi.py
       <Directory /opt/cloud3.0/cloudsec>
       <Files wsgi.py> 
            Require all granted
       </Files>
       </Directory>
       </VirtualHost>



.. end

.. figure:: image/fy-img/add_django.png
   :width: 80%
   :align: center
   :alt: add_django


.. code-block:: console

 4、修改 /opt/cloud3.0/cloudsec/cloudsec/wsgi.py文件 
     新添加 
     import sys
     sys.path.append('/opt/cloud3.0')
     sys.path.append('/opt/cloud3.0/cloudsec')
     path = '/opt/cloud3.0'

      if path not in sys.path:
          sys.path.insert(0, '/opt/cloud3.0')


.. end

.. figure:: image/fy-img/step-4.png
   :width: 80%
   :align: center
   :alt: step-4

.. code-block:: console

 5、令apache2配置生效 
     cd /etc/apache2/sites-available
    执行 
    sudo a2ensite cloudsec 

.. end

.. figure:: image/fy-img/step-5.png
   :width: 80%
   :align: center
   :alt: step-5

执行完后 会在  /etc/apache2/sites-enabled下生成软连接文件 cloudsec.conf


.. figure:: image/fy-img/step-5-1.png
   :width: 80%
   :align: center
   :alt: step-5-1

.. code-block:: console

 6、reload apache2
     执行 ：
     service apache2 reload
     每次修改 配置文件都需要 reload
 7、重启 apache2  service apache2 restart

 8、访问 web


.. end



.. figure:: image/fy-img/step-8.png
   :width: 80%
   :align: center
   :alt: step-8

2
--

.. code-block:: console


.. end


3
-------------


.. code-block:: console



.. end



