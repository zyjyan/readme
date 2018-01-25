=====================================================================
Haking On Docker（P-version）
=====================================================================

Abstract
~~~~~~~~
现在一个明显的趋势是，技术更新迭代的速度非常快。似乎技术一夜之间就火了。之前一直在研究openstack，现在openstack市场及技术趋势均趋于稳定，
DoDocker横空出世。趁有闲暇的时间研究下docker技术。

了解一个技术最权威的地方莫非官网，`Docker.doc <https://docs.docker.com/>`_. 官网首页挂着这样一行字 `Docker provides a way to run applications securely isolated in a container, packaged with all its dependencies and libraries.` 相信大家可以很容易理解，Docker是一种容器技术，该容器可以提供独立的应用，可以提供独立的包依赖以及库函数。如果有部署经验的人应该会产生共鸣。在原有的模式下，解决软件升级以及不同软件之间的依赖冲突，是一件很头大的事情，似乎Docker可以解决这样的问题（如果真的是这样，那么将极大的简化我们的工作量）。且行且探索。

本篇章，结合自己的实践，依据官网提供的素材，进行学习及验证。


.. note::
   文档版本属于探索版本，属于学习记录阶段，随着学习的深入，会定期对前期的内容进行修订。希望读者也可以提供提出建议，共同学习提高。 


.. warning::

   Test.

Contents
~~~~~~~~

.. toctree::
   :maxdepth: 2

   get-docker
   docker-install-ubuntu
   get-started-with-docker
.. Pseudo only directive for each distribution used by the build tool.
   This pseudo only directive for toctree only works fine with Tox.
   When you directly build this guide with Sphinx,
   some navigation menu may not work properly.
.. Keep this pseudo only directive not to break translation tool chain
   at the openstack-doc-tools repo until it is changed.
.. end of contents
