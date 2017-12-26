=================================
        One And Only
=================================

技术回忆录-写给30的自己。


最近一直想找个机会,把过去的工作内容总结一下。也算对过去工作的历程的回顾。
希望能够对将来的一部分工作产生好的影响。工作四年有余，到明年4月即将5年，回顾过去的工作，有些亮点，也有些浪费时间
的地方。

从专业技术纬度上，大致经历了2013年底`nutch+hadoop+solr`平台搭建，
2014-2015年，`openstack`云计算平台技术，2015年-2016年，自研聚合式云安全服务平台，阿里
云计算平台数据对接，2016-2017年，主要忙于安全设备聚合，日志数据处理技术`Logstash+elasticresearch`等；
其中，云计算平台技术最为深入，也对后续的工作帮助最大。也想借此文档，总结好openstack及其相关的
技术(PS：云计算平台技术确实太过庞大，很难用一两句话讲清楚，这也是其入门门槛高的原因)。
The OpenStack system consists of several key services that are separately
installed. These services work together depending on your cloud
needs and include the Compute, Identity, Networking, Image, Block Storage,
Object Storage, Telemetry, Orchestration, and Database services. You
can install any of these projects separately and configure them stand-alone
or as connected entities.

This section describes how to install and configure the OpenStack
Identity service, code-named keystone, on the controller node. For
scalability purposes, this configuration deploys Fernet tokens and
the Apache HTTP server to handle requests.
.. toctree::
   :maxdepth: 3

   index-obs
   index-rdo
   index-ubuntu
