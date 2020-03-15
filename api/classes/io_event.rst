=================
io_event
=================
网络事件回调返回此对象。

命名空间
----------------
``namespace yasio::inet``

成员
-----------------

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - io_event::kind
     - 获取事件类型
   * - io_event::status
     - 返回状态
   * - io_event::packet
     - 获取数据包
   * - io_event::transport
     - 获取事件当前的传输会话句柄
   * - io_event::timestamp
     - 获取事件产生的微秒级时间戳
