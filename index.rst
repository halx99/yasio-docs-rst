yasio 中文文档
^^^^^^^^^^^^^^^^^^

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
* 支持微秒级定时器。
* 支持TCP粘包处理，业务完全不必关心。
* 支持处理由于EINTR信号导致收发数据中断的情形。
* 支持Lua绑定。
* 支持Cocos2d-x jsb绑定。
* 支持CocosCreator jsb2.0绑定。
* 支持Unity3D C#绑定。
* 支持组播。
* 支持SSL客户端，基于OpenSSL。
* 支持非阻塞域名解析，基于c-ares。
* 支持header only集成方式，意味着无需编译，直接像c++ stl一样包含头文件即可使用，使用预编译宏 `YASIO_HEADER_ONLY` 开启。
* 跨平台性:

  * 编译器: 

   * Visual Studio 2013+
   * GCC4.7+
   * xcode9+
   * 其他支持C++11标准的编译器
  * 架构: x86, x64, ARM等。
  * 操作系统: Windows, macOS, Linux, iOS, Android等。

用法一览
----------------------
此简单例子向 ``ip138.com`` 发送http请求并打印返回结果

.. tabs::
 .. code-tab:: cpp

  #include "yasio/yasio.hpp"
  #include "yasio/obstream.hpp"
  using namespace yasio;
  using namespace yasio::inet;
  
  int main()
  {
    io_service service({"www.ip138.com", 80});
    service.set_option(YOPT_S_DEFERRED_EVENT, 0); // 直接在网络线程分派网络事件
    service.start([&](event_ptr&& ev) {
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
  
.. tabs::
 .. code-tab:: lua

  local ip138 = "www.ip138.com"
  local service = yasio.io_service.new({host=ip138, port=80})
  local respdata = ""
  -- 传入网络事件处理函数启动网络服务线程，网络事件有: 消息包，连接响应，连接丢失
  service.start(function(ev)
          local k = ev.kind()
          if (k == yasio.YEK_PACKET) then
              respdata = respdata .. ev:packet():to_string()
          elseif k == yasio.YEK_CONNECT_RESPONSE then
              if ev:status() == 0 then -- status为0表示连接建立成功
                  local transport = ev:transport()
                  local obs = yasio.obstream.new()
                  obs.write_bytes("GET / HTTP/1.1\r\n")
  
                  obs.write_bytes("Host: " .. ip138 .. "\r\n")
  
                  obs.write_bytes("User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.117 Safari/537.36\r\n")
                  obs.write_bytes("Accept: */*;q=0.8\r\n")
                  obs.write_bytes("Connection: Close\r\n\r\n")
  
                  service.write(transport, obs)
              end
          elseif k == yasio.YEK_CONNECTION_LOST then
              print("request finish, respdata: " ..  respdata)
          end
      end)
  -- 将信道0作为TCP客户端打开，并向服务器发起异步连接，进行TCP三次握手
  service.open(0, yasio.YCK_TCP_CLIENT)
  
  -- 由于lua_State和渲染对象，不支持在其他线程操作，因此分派网络事件封装为全局Lua函数，并且以下函数应该在主线程或者游戏引擎渲染线程调用
  function gDispatchNetworkEvent(...)
      service.dispatch(128) -- 每帧最多处理128个网络事件
  end
  
  _G.yservice = service -- Store service to global table as a singleton instance

更多使用实例，请参考 `tests <https://github.com/yasio/yasio/tree/master/tests>`_ & `examples <https://github.com/yasio/yasio/tree/master/tests>`_ :

* tests:

  * `echo_server <https://github.com/yasio/yasio/tree/master/tests/echo_server>`_: TCP/UDP回射服务器
  * `echo_client <https://github.com/yasio/yasio/tree/master/tests/echo_client>`_: TCP, UDP回射客户端
  * `ssltest <https://github.com/yasio/yasio/tree/master/tests/ssl>`_: SSL测试客户端，请求github.com主页并打印返回数据
  * `tcptest <https://github.com/yasio/yasio/tree/master/tests/tcp>`_: TCP测试程序
  * `kcptest <https://github.com/yasio/yasio/tree/master/tests/kcp>`_: KCP测试程序
  * `mcast <https://github.com/yasio/yasio/tree/master/tests/mcast>`_: 组播测试程序

* examples:

  * `ftp_server <https://github.com/yasio/ftp_server>`_: 基于yasio实现的仅支持下载的ftp服务器，`点击 <ftp://ftp.yasio.org/>`_ 访问
  * `lua <https://github.com/yasio/yasio/tree/master/examples/lua>`_: lua样例程序，包含并发http请求，TCP拆包实例代码

构建 tests & examples
----------------------
* 确保已安装支持C++11标准的编译器，例如 ``msvc``, ``gcc``, ``clang`` 等
* 确保已安装 ``git``, ``cmake``
* 运行如下命令:
.. code-block:: sh

  git clone https://github.com/yasio/yasio
  cd yasio
  git submodule update --init --recursive 
  cd build
  # 注意，这里xcode需要运行: cmake .. -GXcode
  cmake ..
  cmake --build . --config Debug

yasio 异步网络服务的三要素
------------------------
* io_channel: 信道，负责连接管理，建立，关闭，重连，启动TCP/UDP服务监听
* io_transport: 传输会话，类似文件句柄，用于实际数据收发，由框架内部产生并通过网络事件返回
* io_service： 核心网络服务，在内部采用 ``反应堆模式(Reactor)`` 处理所有网络事件，对用户提供 ``前置器模式(Proactor)`` 使用方式，即用户只管投递异步消息发送和处理框架返回的消息事件即可，无需处理具体发送和接收细节

API文档
--------------------

.. toctree::
    :maxdepth: 2
    :glob:

    api/index


.. |ImageRelLink| image:: https://img.shields.io/badge/release-v3.33.0-blue.svg
.. _ImageRelLink: https://github.com/yasio/yasio/releases
