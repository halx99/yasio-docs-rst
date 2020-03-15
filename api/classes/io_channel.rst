io_channel
^^^^^^^^^^^^^^^^^^
信道， 负责连接管理，固定对象，用户不可构造, 可通过 ``io_service::cindex_to_handle`` 接口获取

命名空间
---------------------
``namespace yasio::inet``

成员
---------------------
公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`get_service`
     - 获取管理本信道的io_service对象
   * - :ref:`index`
     - 获取索引
   * - :ref:`remote_port`
     - 获取远程端口， 客户端：连接端口，服务器：监听端口

.. _get_service:

io_channel::get_service
-----------------------
获取管理本信道的io_service对象

.. code-block:: cpp

 io_service& get_service()

.. _index:

io_channel::index
-----------------------

.. code-block:: cpp

 int index() const

.. _remote_port:

io_channel::remote_port
-----------------------
| 获取远程端口。
| 客户端：连接端口
| 服务器：监听端口

.. code-block:: cpp

 u_short remote_port() const
