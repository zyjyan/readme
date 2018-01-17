Install Docker
~~~~~~~~~~~~~~~~~~~~~

依据官网的描述，Docker提供两个版本，社区版本以及企业版本。

之前一直以为Docker是免费开源软件，居然还分社区和企业两个版本？那么具体Docker社区版本以及企业版本有何不同？查阅了相关的文档，
果然，企业版本是是从2017年4月17日进行了官网的更新，宣布Docker将支持企业版本。自然，免不了要收费。

社区版本有2中更新的方式, 感觉跟ubuntu操作系统的版本更新有点类似，即 `stable` 以及 `edge`. 版本号的命名规则为:YY:MM. 当前最新版本为17.12.
其中：

# Stable 稳定版本每个季度一次更新；

2. Edge 版本每个月更新一次；

更多的信息可以参考 `Docker Community Edition <https://www.docker.com/community-edition/>`_.


企业版本与社区版本之间的异同
----------------------------
`具体可参考 <https://www.docker.com/pricing>`_.


.. image::./image/docker-diff-version.png



+-----------------------------------------------+------------+-----------------------+----------------+----------------+
| 支持内容                                      | 社区版本   |        企业基础版本   |  企业标准版本  |    企业高级版本|
+-----------------------------------------------+------------+-----------------------+----------------+----------------+
|容器引擎、编排、网络、安全Container engine  and|            |                       |                |                |
|built in orchestration, networking, security   |  yes       |       yes             |        yes     |        yes     |       
+-----------------------------------------------+------------+-----------------------+----------------+----------------+


Plugins and ISV Containers                        No                        yes                   yes              yes 
Docker认证的基础架构，插件，
以及ISV容器


==============================                 ============    =======================  ================  =================

Image Management \
private registry, caching \                        No                       No                   yes          yes
 (Windows and Linux)


==============================                 ============    =======================  ================  =================







Prerequisites
-------------

Before you install and configure the Identity service, you must
create a database.

.. note::

   Before you begin, ensure you have the most recent version of
   ``python-pyasn1`` `installed <https://pypi.python.org/pypi/pyasn1>`_.

#. Use the database access client to connect to the database
   server as the ``root`` user:

   .. code-block:: console

      $ mysql -u root -p

   .. end

2. Create the ``keystone`` database:

   .. code-block:: console

      MariaDB [(none)]> CREATE DATABASE keystone;

   .. end

#. Grant proper access to the ``keystone`` database:

   .. code-block:: console

      MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
      IDENTIFIED BY 'KEYSTONE_DBPASS';
      MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
      IDENTIFIED BY 'KEYSTONE_DBPASS';

   .. end

   Replace ``KEYSTONE_DBPASS`` with a suitable password.

#. Exit the database access client.

.. _keystone-install-configure-obs:

Install and configure components
--------------------------------

.. include:: shared/note_configuration_vary_by_distribution.rst

.. note::

   This guide uses the Apache HTTP server with ``mod_wsgi`` to serve
   Identity service requests on ports 5000 and 35357. By default, the
   keystone service still listens on these ports. Therefore, this guide
   manually disables the keystone service.

.. note::

    Starting with the Newton release, SUSE OpenStack packages are shipping
    with the upstream default configuration files. For example
    ``/etc/keystone/keystone.conf``, with customizations in
    ``/etc/keystone/keystone.conf.d/010-keystone.conf``. While the
    following instructions modify the default configuration file, adding a
    new file in ``/etc/keystone/keystone.conf.d`` achieves the same
    result.

#. Run the following command to install the packages:

   .. code-block:: console

      # zypper install openstack-keystone apache2-mod_wsgi

   .. end

2. Edit the ``/etc/keystone/keystone.conf`` file and complete the following
   actions:

   * In the ``[database]`` section, configure database access:

     .. path /etc/keystone/keystone.conf
     .. code-block:: ini

        [database]
        # ...
        connection = mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone

     .. end

     Replace ``KEYSTONE_DBPASS`` with the password you chose for the database.

     .. note::

        Comment out or remove any other ``connection`` options in the
        ``[database]`` section.

   * In the ``[token]`` section, configure the Fernet token provider:

     .. path /etc/keystone/keystone.conf
     .. code-block:: ini

        [token]
        # ...
        provider = fernet

     .. end

3. Populate the Identity service database:

   .. code-block:: console

      # su -s /bin/sh -c "keystone-manage db_sync" keystone

   .. end

4. Initialize Fernet key repositories:

   .. code-block:: console

      # keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
      # keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

   .. end

5. Bootstrap the Identity service:

   .. code-block:: console

      # keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
        --bootstrap-admin-url http://controller:35357/v3/ \
        --bootstrap-internal-url http://controller:5000/v3/ \
        --bootstrap-public-url http://controller:5000/v3/ \
        --bootstrap-region-id RegionOne

   .. end

   Replace ``ADMIN_PASS`` with a suitable password for an administrative user.

Configure the Apache HTTP server
--------------------------------

#. Edit the ``/etc/sysconfig/apache2`` file and configure the
   ``APACHE_SERVERNAME`` option to reference the controller node:

   .. path /etc/sysconfig/apache2
   .. code-block:: shell

      APACHE_SERVERNAME="controller"

   .. end

#. Create the ``/etc/apache2/conf.d/wsgi-keystone.conf`` file
   with the following content:

   .. path /etc/apache2/conf.d/wsgi-keystone.conf
   .. code-block:: apache

      Listen 5000
      Listen 35357

      <VirtualHost *:5000>
          WSGIDaemonProcess keystone-public processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
          WSGIProcessGroup keystone-public
          WSGIScriptAlias / /usr/bin/keystone-wsgi-public
          WSGIApplicationGroup %{GLOBAL}
          WSGIPassAuthorization On
          ErrorLogFormat "%{cu}t %M"
          ErrorLog /var/log/apache2/keystone.log
          CustomLog /var/log/apache2/keystone_access.log combined

          <Directory /usr/bin>
              Require all granted
          </Directory>
      </VirtualHost>

      <VirtualHost *:35357>
          WSGIDaemonProcess keystone-admin processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
          WSGIProcessGroup keystone-admin
          WSGIScriptAlias / /usr/bin/keystone-wsgi-admin
          WSGIApplicationGroup %{GLOBAL}
          WSGIPassAuthorization On
          ErrorLogFormat "%{cu}t %M"
          ErrorLog /var/log/apache2/keystone.log
          CustomLog /var/log/apache2/keystone_access.log combined

          <Directory /usr/bin>
              Require all granted
          </Directory>
      </VirtualHost>

   .. end

#. Recursively change the ownership of the ``/etc/keystone`` directory:

   .. code-block:: console

      # chown -R keystone:keystone /etc/keystone

   .. end

Finalize the installation
-------------------------

#. Start the Apache HTTP service and configure it to start when the system
   boots:

   .. code-block:: console

      # systemctl enable apache2.service
      # systemctl start apache2.service

   .. end

2. Configure the administrative account

   .. code-block:: console

      $ export OS_USERNAME=admin
      $ export OS_PASSWORD=ADMIN_PASS
      $ export OS_PROJECT_NAME=admin
      $ export OS_USER_DOMAIN_NAME=Default
      $ export OS_PROJECT_DOMAIN_NAME=Default
      $ export OS_AUTH_URL=http://controller:35357/v3
      $ export OS_IDENTITY_API_VERSION=3

   .. end

   Replace ``ADMIN_PASS`` with the password used in the
   ``keystone-manage bootstrap`` command in `keystone-install-configure-obs`_.
