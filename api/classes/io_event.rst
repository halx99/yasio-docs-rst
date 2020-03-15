io_event
^^^^^^^^^^^^^^^^^
网络事件回调返回此对象。

命名空间
--------------
``namespace yasio::inet``

成员
--------------

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`kind`
     - 获取事件类型
   * - :ref:`status`
     - 返回状态
   * - :ref:`packet`
     - 获取数据包
   * - :ref:`transport`
     - 获取事件当前的传输会话句柄
   * - :ref:`timestamp`
     - 获取事件产生的微秒级时间戳

.. _kind:

io_event::kind
--------------
获取事件类型

.. code-block:: cpp

 int kind() const

Return Value
>>>>>>>>>>>>>>>
* ``YEK_PACKET`` : 消息事件
* ``YEK_CONNECT_RESPONSE`` : 连接响应事件
* ``YEK_CONNECTION_LOST`` : 连接断开事件


.. _status:

io_event::status
----------------
获取事件类型

.. code-block:: cpp

 int status() const

Return Value
>>>>>>>>>>>>>>>
| 0: 正常， 非0： 出错
| 业务只需要处理0和非0的情况，无需关心具体状态码，非0时可选择做简单打印记录

.. _packet:

io_event::packet
-----------------
获取事件中的完整消息包

.. code-block:: cpp

 std::vector<char>& packet()

.. _transport:

io_event::transport
-------------------
获取事件到传输会话句柄

.. code-block:: cpp

 transport_handle_t transport()


.. _timestamp:

io_event::timestamp
-------------------
返回事件产生的微秒级时间戳

.. code-block:: cpp

 long long timestamp() const
