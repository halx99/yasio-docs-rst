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
   * - :ref:`timestamp`
     - 获取事件产生的微秒级时间戳
   * - :ref:`kind`
     - 获取事件类型
   * - :ref:`status`
     - 返回状态
   * - :ref:`packet`
     - 获取数据包
   * - :ref:`transport`
     - 获取事件当前的传输会话句柄
  * - :ref:`transport_id`
     - 获取trasnport唯一ID
  * - :ref:`transport_udata`
     - 获取或设置transport的用户数据

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

.. _transport_id:

io_event::transport_id()
-----------------------
获取传输会话ID，可用于日志跟踪

.. code-block:: cpp

 unsigned int id() const

Return Value
>>>>>>>>>>>>>>>>>>>>
返回ID是全局自增的，可保证在32位整数最大范围内保证唯一, (uint32_t)-1 视为无效ID

.. _transport_udata:

io_event::transport_udata
--------------------------
安全地获取和设置用户变量，所有类型网络事件，调用次函数都是安全的

.. code-block:: cpp

 _Uty io_event::transport_udata()
 void io_event::transport_udata(_Uty uservalue)

Remark
>>>>>>>>>>>>>
注意: 生命周期需要使用者自己维护，收到connect success存储userdata, 收到connect lost事件，清理userdata

