yasio 文档
====================

*yasio 是一个轻量级跨平台的异步socket库，专注于客户端和基于各种游戏引擎的游戏客户端网络服务, 支持windows & linux & apple & android & win10-universal。*

|ImageRelLink|_

* `yasio GitHub <https://github.com/yasio/yasio>`_
* yasio 文档

  + `English <https://docs.yasio.org/en/latest/>`_
  + `简体中文 <https://docs.yasio.org/zh_CN/latest/>`_
  + `下载PDF <https://readthedocs.org/projects/yasio-docs/downloads/pdf/latest/>`_

* yasio官网：`https://yasio.org/ <https://yasio.org/>`_

成功案例
--------------------
`红警OL手游项目 <https://hjol.qq.com/>`_: 用于客户端网络传输，并且随着该项目于2018年10月17日由腾讯游戏发行正式上线后稳定运行于上千万移动设备上。

特点
--------------------
yasio 借鉴著名的boost网络库asio, 在保持轻量级的情况下，具备以下特点:

* 支持IPv6_only网络。
* 支持处理多个连接的所有网络事件。
* 支持微妙级计时器。
* 支持TCP粘包处理，业务完全不必关心。
* 支持处理由于EINTR信号导致收发数据中断的情形。
* 支持Lua绑定。
* 支持Cocos2d-x jsb绑定。
* 支持CocosCreator jsb2.0绑定。
* 支持Unity3D C#绑定。
* 支持组播。
* 支持SSL客户端，基于OpenSSL。
* 支持非阻塞域名解析，基于c-ares。
* 支持header only集成方式，意味着无需编译，直接像c++ stl一样包含头文件即可使用，使用 `YASIO_HEADER_ONLY` 开启。
* 跨平台性:

  * 编译器: Visual Studio 2013或更高版本，GCC4.8或更高版本, xcode9或更高版本，其他支持C++11标准的编译器。
  * 架构: x86, x64, ARM等。
  * 操作系统: Windows, macOS, Linux, iOS, Android等。

用法一览
----------------------
此简单例子向ip138.com发送http请求并打印返回结果

.. code-block:: cpp

 #include "yasio/yasio.hpp"
 #include "yasio/obstream.hpp"
 using namespace yasio;
 using namespace yasio::inet;
 
 int main()
 {
   io_service service({"www.ip138.com", 80});
   service.set_option(YOPT_S_DEFERRED_EVENT, 0); // 直接在网络线程分派网络事件
   service.start_service([&](event_ptr&& ev) {
     switch (ev->kind())
     {
       case YEK_PACKET: {
         auto packet = std::move(ev->packet());
         fwrite(packet.data(), packet.size(), 1, stdout);
         fflush(stdout);
         break;
       }
       case YEK_CONNECT_RESPONSE:
         if (ev->status() == 0)
         {
           auto transport = ev->transport();
           if (ev->cindex() == 0)
           {
             obstream obs;
             obs.write_bytes("GET /index.htm HTTP/1.1\r\n");
 
             obs.write_bytes("Host: www.ip138.com\r\n");
 
             obs.write_bytes("User-Agent: Mozilla/5.0 (Windows NT 10.0; "
                             "WOW64) AppleWebKit/537.36 (KHTML, like Gecko) "
                             "Chrome/79.0.3945.117 Safari/537.36\r\n");
             obs.write_bytes("Accept: */*;q=0.8\r\n");
             obs.write_bytes("Connection: Close\r\n\r\n");
 
             service.write(transport, std::move(obs.buffer()));
           }
         }
         break;
       case YEK_CONNECTION_LOST:
         printf("The connection is lost.\n");
         break;
     }
   });
   // open channel 0 as tcp client
   service.open(0, YCK_TCP_CLIENT);
   getchar();
 }

更多使用实例，请参考 `tests <https://github.com/yasio/yasio/tree/master/tests>`_ & `examples <https://github.com/yasio/yasio/tree/master/tests>`_ :

* tests:

  * `echo_server <https://github.com/yasio/yasio/tree/master/tests/echo_server>`_: TCP/UDP回射服务器
  * `echo_client <https://github.com/yasio/yasio/tree/master/tests/echo_client>`_: TCP, UDP回射客户端
  * `ssltest <https://github.com/yasio/yasio/tree/master/tests/ssl>`_: SSL测试客户端，请求github.com主页并打印返回数据
  * `tcptest <https://github.com/yasio/yasio/tree/master/tests/tcp>`_: TCP测试程序
  * `kcptest <https://github.com/yasio/yasio/tree/master/tests/kcp>`_: KCP测试程序
  * `mcast <https://github.com/yasio/yasio/tree/master/tests/mcast>`_: 组播测试程序

* examples:

  * `ftp_server <https://github.com/yasio/yasio/tree/master/example/ftp_server>`_: 基于yasio实现的仅支持下载的ftp服务器，`点击 <ftp://ftp.x-studio.net>`_ 访问
  * `lua <https://github.com/yasio/yasio/tree/master/example/ftp_server>`_: lua样例程序，包含并发http请求，TCP拆包实例代码

构建 tests & examples
----------------------
* 确保安装 ``git``, ``cmake``, ``VS2013或更高版本``, ``gcc-4.8以上版本``, ``xcode9或以上版本``
* 运行如下命令:
.. code-block:: sh

  git clone https://github.com/yasio/yasio
  cd yasio
  git submodule update --init --recursive 
  cd build
  cmake ..
  cmake --build . --config Debug

API文档:
^^^^^

.. toctree::
    :maxdepth: 2
    :glob:

    api/index


.. |ImageRelLink| image:: https://img.shields.io/badge/release-v3.33.0-blue.svg
.. _ImageRelLink: https://github.com/yasio/yasio/releases
