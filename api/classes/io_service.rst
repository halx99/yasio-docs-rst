io_service
===============================
yasio的核心类，提供TCP,UDP,KCP异步网络服务，以独立线程处理所有网络事件。


命名空间
---------------------
``namespace yasio::inet``

成员
-----------------

公共构造函数:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`io_service::io_service <io_service>`
     - 构造io_service对象

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - io_service::start_service
     - 启动网络服务线程
   * - io_service::stop_service
     - 停止网络服务线程
   * - io_service::is_running
     - 判断网络线程是否运行
   * - io_service::dispatch
     - 在调用者线程分派网络事件（包，连接响应，连接丢失）
   * - io_service::set_option
     - 设置选项
   * - io_service::open
     - 打开信道
   * - io_service::reopen
     - 重新打开Transport
   * - io_service::close
     - 关闭信道或Transport
   * - io_service::write
     - 异步写入数据
   * - io_service::write_to
     - 异步写入数据, 仅用于非绑定状态的UDP
   * - io_service::schedule
     - 启动定时器
   * - io_service::builtin_resolv
     - 内置域名解析
   * - io_service::cindex_to_channel
     - 根据信道索引获取信道对象  


.. _io_service:
io_service::io_service
------------------
构造io_service对象，共有4个重载版本

.. code-block:: cpp

 io_service::io_service()
 io_service::io_service(int channel_count)
 io_service::io_service(const io_hostent& channel_eps)
 io_service::io_service(const io_hostent* channel_eps, int channel_count)

Example
^^^^^^^^^^^^^^^^^^
.. tabs::
 .. code-tab:: cpp

  io_service s1; // s1对象仅支持1个信道
  io_service s2(5); // s2对象仅支持5个信道并发网络处理，即可以同时处理5个客户端连接或开5个TCP server
  io_service s3(io_hostent{"github.com", 443}); // s3对象仅支持1个信道
  io_hostent hosts[] = {
    {"192.168.1.66", 20336},
    {"192.168.1.88", 20337},
  };
  io_service s4(hosts, YASIO_ARRAYSIZE(hosts)); // s4支持2个信道


