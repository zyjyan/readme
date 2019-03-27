=====================================================================
Haking On FLUME（P-version）
=====================================================================

Abstract
~~~~~~~~
Flume的官方网址为 `Flume.DOC <http://flume.apache.org/>`_. 

Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data. It has a simple and flexible architecture based on streaming data flows. It is robust and fault tolerant with tunable reliability mechanisms and many failover and recovery mechanisms. It uses a simple extensible data model that allows for online analytic application.


.. figure:: flume/image/flume/flume_index.png
   :width: 80%
   :align: center
   :alt: flume_index

本篇章，结合自己的实践，依据官网提供的素材，进行学习及验证。


.. note::
   文档版本属于探索版本，属于学习记录阶段，随着学习的深入，会定期对前期的内容进行修订。希望读者也可以提供提出建议，共同学习提高。 


.. warning::

   搭建环境时Flume版本为apache-flume-1.9.0-bin.tar.gz. 操作系统版本为ubuntu.16.04lts。

Contents
~~~~~~~~

.. toctree::
   :maxdepth: 2

   flume/flume-index.rst
..   kafka/kafka-quickstart.rst
..   kafka/kafka-distribution.rst
.. Pseudo only directive for each distribution used by the build tool.
   This pseudo only directive for toctree only works fine with Tox.
   When you directly build this guide with Sphinx,
   some navigation menu may not work properly.
.. Keep this pseudo only directive not to break translation tool chain
   at the openstack-doc-tools repo until it is changed.
.. end of contents
