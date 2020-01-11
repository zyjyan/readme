horizon+keystone+ceilometer
~~~~~~~~~~~~~~~~~~~~~~~~~~~

为何选择3个框架
----------------

主要原因如下：

.. code-block:: console

 # 懒
 # 熟悉
 # 还不错

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



.. end

.. figure:: image/linux-shell/awk.png
   :width: 80%
   :align: center
   :alt: awk


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



