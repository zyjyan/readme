=====================================================================
Haking On Atlas
=====================================================================

Abstract
~~~~~~~~

.. note::

   文档版本属于探索版本，属于学习记录阶段，随着学习的深入，会定期对前期的内容进行修订。希望读者也可以提供提出建议，共同学习提高。 
   如官方文档所言：
   Atlas is a scalable and extensible set of core foundational governance services – enabling enterprises to effectively and efficiently meet their compliance requirements within Hadoop and allows integration with the whole enterprise data ecosystem.Apache Atlas provides open metadata management and governance capabilities for organizations to build a catalog of their data assets, classify and govern these assets and provide collaboration capabilities around these data assets for data scientists, analysts and the data governance team.

   几个关键词：数据发现、数据跟踪、数据血缘、数据标签、数据分级分类。


.. warning::

   选择Hadoop版本为hadoop-2.7.7. 操作系统版本为ubuntu.16.04lts hive 版本为2.3.4, atlas当前的版本为2.0。
   关于版本的选择，请充分评估与基于hadoop相关的大数据软件的兼容性。

Contents
~~~~~~~~

.. toctree::
   :maxdepth: 5

   atlas/atlas-install.rst
.. Pseudo only directive for each distribution used by the build tool.
   This pseudo only directive for toctree only works fine with Tox.
   When you directly build this guide with Sphinx,
   some navigation menu may not work properly.
.. Keep this pseudo only directive not to break translation tool chain
   at the openstack-doc-tools repo until it is changed.
.. end of contents
