===============
io_channel
===============
信道， 负责连接管理，固定对象，用户不可构造, 可通过io_service::cindex_to_handle获取

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
   * - io_channel::get_service
     - 获取管理本信道的io_service对象
   * - io_channel::index
     - 获取索引
   * - io_channel::remote_port
     - 获取远程端口， 客户端：连接端口，服务器：监听端口
