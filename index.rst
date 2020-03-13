yasio 文档
====================

*yasio 是一个轻量级跨平台的异步socket库，专注于客户端和基于各种游戏引擎的游戏客户端网络服务, 支持windows & linux & apple & android & win10-universal。*

|ImageRelLink|_ |OtherBuildStatus|_ |WinBuildStatus|_ 

* `yasio GitHub <https://github.com/yasio/yasio>`_
* yasio 文档

  + `English <https://docs.yasio.org/en/latest/>`_
  + `简体中文 <https://docs.yasio.org/zh_CN/latest/>`_
  + `下载PDF <https://readthedocs.org/projects/yasio-docs/downloads/pdf/latest/>`_

* yasio官网：`https://yasio.org/ <https://yasio.org/>`_

yasio 成功案例
--------------------
`红警OL手游项目 <https://hjol.qq.com/>`_: 用于客户端网络传输，并且随着该项目于2018年10月17日由腾讯游戏发行正式上线后稳定运行于上千万移动设备上。

yasio 特点
--------------------
yasio 借鉴著名的boost网络库asio, 在保持轻量级的情况下，具备以下特点:

* 支持header only集成方式，意味着无需编译，直接像stl一样包含头文件即可使用。
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

更多使用实例，请参考 `tests <https://github.com/yasio/yasio/tests/>`_ & `examples <https://github.com/yasio/yasio/examples/>`_


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

.. |ImageRelLink| image:: https://img.shields.io/badge/release-v3.31.3-blue.svg
.. _ImageRelLink: https://github.com/yasio/yasio/releases

.. |OtherBuildStatus| image:: https://travis-ci.com/yasio/yasio.svg?branch=master
.. _OtherBuildStatus: https://github.com/yasio/yasio/releases

.. |WinBuildStatus| image:: (https://ci.appveyor.com/api/projects/status/d6qjfygtw2ewt9pf/branch/master?svg=true
.. _WinBuildStatus: https://ci.appveyor.com/project/halx99/yasio