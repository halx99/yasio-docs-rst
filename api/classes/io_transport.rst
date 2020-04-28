io_transport
^^^^^^^^^^^^^^^^^^
传输会话，负责数据传输，通过io_event::transport接口中获取。

.. note:: io_transport所有公共方法，如需调用，请在使用接口 ``io_service::is_open`` 返回true时调用。


命名空间
---------------------
``namespace yasio::inet``

成员
---------------------
公共成员:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 变量名
     - 说明
   * - :ref:`ud_`
     - 用户数据


公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`id`
     - 获取传输会话ID
   * - :ref:`local_endpoint`
     - 获取本地地址
   * - :ref:`peer_endpoint`
     - 获取对端地址
   * - :ref:`get_context`
     - 获取管理会话的信道


.. _ud_:

io_transport::ud_
-----------------------
可通过此变量设置用户数据

.. code-block:: cpp

  union
  {
    void* ptr;
    int ival;
  } ud_;

Remark
>>>>>>>>>>>>>
注意: 生命周期需要使用者自己维护，收到connect success存储userdata, 收到connect lost事件，清理userdata

.. _id:

io_transport::id
-----------------------
获取传输会话ID，可用于日志跟踪

.. code-block:: cpp

 unsigned int id() const

Return Value
>>>>>>>>>>>>>>>>>>>>
返回ID是全局自增的，可保证在32位整数最大范围内保证唯一

.. _local_endpoint:

io_transport::local_endpoint
-----------------------
返回本地IP和端口

.. code-block:: cpp

 ip::endpoint local_endpoint() const


.. _peer_endpoint:

io_transport::peer_endpoint
-----------------------
返回通信对端IP和端口

.. code-block:: cpp

 ip::endpoint peer_endpoint() const


.. _get_context:

io_transport::get_context
-----------------------
获取管理会话的信道对象

.. code-block:: cpp

 io_channel* get_context() const

