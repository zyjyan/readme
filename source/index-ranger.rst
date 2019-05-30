=====================================================================
Haking On Ranger
=====================================================================

Abstract
~~~~~~~~

.. note::

   文档版本属于探索版本，属于学习记录阶段，随着学习的深入，会定期对前期的内容进行修订。希望读者也可以提供提出建议，共同学习提高。 
   如官方文档所言：Apache Ranger™ is a framework to enable, monitor and manage comprehensive data security across the Hadoop platform.
   The vision with Ranger is to provide comprehensive security across the Apache Hadoop ecosystem. With the advent of Apache YARN, the Hadoop platform can now support a true data lake architecture. Enterprises can potentially run multiple workloads, in a multi tenant environment. Data security within Hadoop needs to evolve to support multiple use cases for data access, while also providing a framework for central administration of security policies and monitoring of user access.

   几个关键词：权限访问控制，基于hadoop生态，多用户模型。
   
   涉及到访问控制安全问题，大致可以归结为"用户-资源-权限"之间的关系。也就是用户在有限授权下访问资源。这里的用户可以是个体，也可以是由个体组成的用户组。资源可以是计算资源、数据资源、存储资源、网络资源等。权限可以基于角色、基于标签等进行产品设计，但最终，都要归结为访问控制的策略，更抽象的定义为，权限是执行策略的集合。

  在工程技术实现上，与访问控制相辅相成的往往有对访问控制行为及结果的审计，以确保访问控制策略生效或可对用户访问资源的行为可追溯。
  
  具体到对数据的访问控制，本质上产生该问题的原因应该是数据所有权和数据使用权的分离。也就是说，数据的归属权是固定个人或者组织的，但数据的使用权可通过某种协议约束或者协商后，归多个个人或者组织使用。站在产品设计的角度，对于数据的访问控制、用户及组织的管理、协商流程管理、元数据发现、元数据管理、元数据的标签管理、数据血缘、基于标签的访问控制策略、元数据的分级分类(该问题可以抽象为对元数据的标签管理)、元数据搜索等。

 访问控制，在原理上，需要在用户访问资源的必经之路(所有路径)上做访问资源的策略检查，在大数据场景下，atlas通过hook机制，实现对元数据的管理（发现和更新）、ranger通过hook机制，实现对数据资源的访问控制，atlas+ranger的方案，可以实现基于标签的动态访问控制能力。基于hook机制，对于数据的安全管理设计上，有很大的想象空间。




.. warning::

   选择Hadoop版本为hadoop-2.7.7. 操作系统版本为ubuntu.16.04lts hive 版本为2.3.4, ranger当前的版本为1.2。
   关于版本的选择，请充分评估与基于hadoop相关的大数据软件的兼容性。

Contents
~~~~~~~~

.. toctree::
   :maxdepth: 5

   ranger/ranger-install.rst
.. Pseudo only directive for each distribution used by the build tool.
   This pseudo only directive for toctree only works fine with Tox.
   When you directly build this guide with Sphinx,
   some navigation menu may not work properly.
.. Keep this pseudo only directive not to break translation tool chain
   at the openstack-doc-tools repo until it is changed.
.. end of contents
