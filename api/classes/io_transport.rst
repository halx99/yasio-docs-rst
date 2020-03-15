======================
io_transport
======================
传输会话，负责数据传输，通过io_event::transport接口中获取。

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
   * - io_transport::is_open
     - 判断传输会话是否打开，只有打开状态才能正常收发数据
   * - io_transport::local_endpoint
     - 获取本地地址
   * - io_transport::peer_endpoint
     - 获取对端地址
   * - io_transport::cindex
     - 获取传输会话的信道索引
   * - io_transport::status
     - 获取传输会话状态
   * - io_transport::fetch_packet
     - 取走传输会话中的数据包
   * - io_transport::id
     - 获取传输会话ID，可用于日志跟踪
   * - io_transport::get_service
     - 获取管理会话的io_service对象