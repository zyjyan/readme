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

 for i in `ps -aux|grep ping |awk '{print $2}'`;  do kill -9 $i; done; 

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

.. Note::
   
   For KAFKA。
