io_service
===============================
yasio的核心类，提供TCP,UDP,KCP异步网络服务，以独立线程处理所有网络事件。


命名空间
---------------------
namespace yasio::inet

成员
-----------------

公共构造函数:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - io_service::io_service()
     - 构造一个仅支持一个信道的io_service对象
   * - io_service::io_service(int channel_count)
     - 构造支持指定信道个数的io_service对象，注意不能超过系统定义的select支持最大值
   * - io_service::io_service(const io_hostent& channel_eps)
     - 构造一个仅支持一个信道的io_service对象, 
       并初始化信道ip和端口
   * - io_service::io_service(const io_hostent* channel_eps, int channel_count)
     - 根据ip和端口列表构造支持相应信道个数的io_service对象。