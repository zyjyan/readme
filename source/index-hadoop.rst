=====================================================================
Haking On Hadoop（P-version）
=====================================================================

Abstract
~~~~~~~~
Hadoop的官方网址为 `HADOOP.DOC <http://hadoop.apache.org/>`_. 

Hadoop `Hadoop project develops open-source software for reliable, scalable, distributed computing.` 

The Apache Hadoop software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.

本篇章，结合自己的实践，依据官网提供的素材，进行学习及验证。


.. note::
   文档版本属于探索版本，属于学习记录阶段，随着学习的深入，会定期对前期的内容进行修订。希望读者也可以提供提出建议，共同学习提高。 


.. warning::

   选择Hadoop版本为hadoop-2.7.7. 操作系统版本为ubuntu.16.04lts。
   关于版本的选择，请充分评估与基于hadoop相关的大数据软件的兼容性。

Contents
~~~~~~~~

.. toctree::
   :maxdepth: 2

   hadoop/hadoop-install.rst
.. Pseudo only directive for each distribution used by the build tool.
   This pseudo only directive for toctree only works fine with Tox.
   When you directly build this guide with Sphinx,
   some navigation menu may not work properly.
.. Keep this pseudo only directive not to break translation tool chain
   at the openstack-doc-tools repo until it is changed.
.. end of contents
