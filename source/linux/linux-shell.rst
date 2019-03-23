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



.. figure:: image/kafka/log_anatomy.png
   :width: 80%
   :align: center
   :alt: log_anatomy





.. Note::
   
   For KAFKA。
