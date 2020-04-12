io_service
^^^^^^^^^^^^^^^^^^
yasio的核心类，提供TCP,UDP,KCP异步网络服务，以独立线程处理所有网络事件。

命名空间
---------
``namespace yasio::inet``

成员
-----------------

公共构造函数:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`io_service`
     - 构造io_service对象

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - :ref:`start`
     - 启动网络服务
   * - :ref:`stop`
     - 停止网络服务
   * - :ref:`is_running`
     - 判断网络线程是否运行
   * - :ref:`dispatch`
     - 在调用者线程分派网络事件（包，连接响应，连接丢失）
   * - :ref:`set_option`
     - 设置选项
   * - :ref:`open`
     - 打开信道
   * - :ref:`is_open`
     - 判断信道或传输会话是否已打开
   * - :ref:`reopen`
     - 重新打开Transport
   * - :ref:`close`
     - 关闭信道或Transport
   * - :ref:`write`
     - 异步写入数据
   * - :ref:`write_to`
     - 异步写入数据, 仅用于非绑定状态的UDP
   * - :ref:`schedule`
     - 启动定时器
   * - :ref:`builtin_resolv`
     - 内置域名解析
   * - :ref:`channel_at`
     - 根据信道索引获取信道对象  


.. _io_service:

io_service::io_service
-----------------------
构造io_service对象，共有4个重载版本

.. code-block:: cpp

 io_service::io_service()
 io_service::io_service(int channel_count)
 io_service::io_service(const io_hostent& channel_eps)
 io_service::io_service(const io_hostent* channel_eps, int channel_count)

Example
>>>>>>>>>>>>>>>>>>
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


.. _start:

io_service::start
-------------------------
启动网络服务

.. code-block:: cpp

 void start(io_event_cb_t cb)

Parameters
>>>>>>>>>>>>>>>>>>
| *cb*
| 接收网络事件的回调函数

Example
>>>>>>>>>>>>>>>>>>
.. tabs::
 .. code-tab:: cpp

  auto service = yasio_shared_service(io_hostent{host="ip138.com", port=80});
  service->start([](event_ptr&& ev) {
    auto kind = ev->kind();
    if (kind == YEK_CONNECT_RESPONSE)
    {
      if (ev->status() == 0)
        printf("[%d] connect succeed.\n", ev->cindex());
      else
        printf("[%d] connect failed!\n", ev->cindex());
    }
  });

.. _stop:

io_service::stop
------------------------
停止网络服务

.. code-block:: cpp

 void stop()

.. _is_running:

io_service::is_running
----------------------
判断网络服务是否运行

.. code-block:: cpp

 bool is_running() const

.. _dispatch:

io_service::dispatch
--------------------
在调用者线程分派网络事件

.. code-block:: cpp

 void dispatch(int max_count)

Parameters
>>>>>>>>>>>>>>>>>>
| *max_count*
| 每次调用分派最大网络事件数, 通常128足够

Example
>>>>>>>>>>>>>>>>>>
.. tabs::
 .. code-tab:: cpp

  // 通常在OpenGL或cocos和unity等游戏引擎渲染线程调用，
  // 以便在特定网络消息回调里安全地更新界面逻辑。
  yasio_shared_service()->dispatch(128); 

.. _set_option:

io_service::set_option
-----------------------
设置选项, 是可变参接口，根据opt不同，参数个数和类型不同

.. code-block:: cpp

 void set_option(int opt, ...)

Parameters
>>>>>>>>>>>>>>>>>>
| *opt*
| 选项类型，以 ``YOPT_`` 开头的枚举值, 详见: :ref:`options`

Example
>>>>>>>>>>>>>>>>>>
.. tabs::
 .. code-tab:: cpp
  
  io_hostent hosts[] = {
    {"192.168.1.66", 20336},
    {"192.168.1.88", 20337},
  };
  io_service* service = new io_service(hosts, YASIO_ARRAYSIZE(hosts));
  
  // 对于有包长度字段的协议，对于tcp自定义二进制协议，强烈建议设计包长度字段，并设置此选项，业务无须关心粘包问题
  service->set_option(YOPT_C_LFBFD_PARAMS,
                      0,     // channelIndex, 信道索引
                      65535, // maxFrameLength, 最大包长度
                      0,     // lenghtFieldOffset, 长度字段偏移，相对于包起始字节
                      4, // lengthFieldLength, 长度字段大小，支持1字节，2字节，3字节，4字节
                      0 // lengthAdjustment：如果长度字段字节大小包含包头，则为0， 否则，这里=包头大小
  );

  // 对于没有包长度字段设计的协议，例如http， 设置包长度字段为-1，
  // 那么底层服务收到多少字节就会传回给上层多少字节
  service->set_option(YOPT_C_LFBFD_PARAMS, 1, 65535, -1, 0, 0);

.. _open:

io_service::open
------------------
打开信道

.. code-block:: cpp

 void open(size_t cindex, int kind)

Parameters
>>>>>>>>>>>>>>>>>>
| *cindex*
| 信道索引
| 
| *kind*
| 信道类型，可取值: 

 * ``YCK_TCP_CLIENT``
 * ``YCK_TCP_SERVER``
 * ``YCK_UDP_CLIENT``
 * ``YCK_UDP_SERVER``
 * ``YCK_KCP_CLIENT``
 * ``YCK_KCP_SERVER``
 * ``YCK_SSL_CLIENT``

Example
>>>>>>>>>>>>>>>>>>
.. tabs::
 .. code-tab:: cpp

  // 将信道0作为TCP客户端打开，发起TCP三次握手和服务器建立连接
  yasio_shared_service()->open(0, YCK_TCP_CLIENT); 

.. _is_open:

io_service::is_open
----------------------
判断信道或传输会话是否已打开, 共有2个重载版本，分别用于信道和传输会话

.. code-block:: cpp

 bool is_open(transport_handle_t) const
 bool is_open(int cindex) const

.. _reopen:

io_service::reopen
------------------
重新打开传输会话

.. code-block:: cpp

 void reopen(transport_handle_t transport)

Parameters
>>>>>>>>>>>>>>>>>>
| *transport*
| 传输会话


.. _close:

io_service::close
------------------
关闭信道或传输会话

.. code-block:: cpp

 void close(transport_handle_t transport)
 void close(int cindex)

Parameters
>>>>>>>>>>>>>>>>>>
| *transport*
| 传输会话句柄


.. _write:

io_service::write
------------------
异步发送数据

.. code-block:: cpp

 int write(transport_handle_t thandle, std::vector<char> buffer,
                        std::function<void()> completion_handler = nullptr)

Parameters
>>>>>>>>>>>>>>>>>>
| *thandle*
| 传输会话句柄
| 
| *buffer*
| 要发送的数据
| 
| *completion_handler*
| 发送完成回调，对于未绑定类udp传输会话，会使用默认远端地址


.. _write_to:

io_service::write_to
---------------------
非阻塞发送UDP数据

.. code-block:: cpp

 int write_to(transport_handle_t thandle, std::vector<char> buffer,
                           const ip::endpoint& to, std::function<void()> completion_handler = nullptr)

Parameters
>>>>>>>>>>>>>>>>>>
| *thandle*
| 传输会话句柄
| 
| *buffer*
| 要发送的数据
| 
| *to*
| 发送远端地址
|
| *completion_handler*
| 发送完成回调, kcp暂不支持此回调

Remark
>>>>>>>>>>>>>>>>>>
只能用于未使用connect绑定过4元组的UDP socket.


.. _schedule:

io_service::schedule
---------------------
注册定时器

.. code-block:: cpp

 highp_timer_ptr schedule(const std::chrono::microseconds& duration, timer_cb_t cb)

Parameters
>>>>>>>>>>>>>>>>>>
| *duration*
| 指定定时器超时时间
| 
| *cb*
| 定时器超时回调函数

Return Value
>>>>>>>>>>>>>>>>>>
定时器引用计数的智能指针， 用户可持有此指针对定时器进行操作

Example
>>>>>>>>>>>>>>>>>>
.. tabs::
 .. code-tab:: cpp

  // 注册一个3秒后超时的一次性计时器，超时后定时器会被自动销毁
  yasio_shared_service()->schedule(std::chrono::seconds(3), []()->bool{
    printf("time called!\n");
    return true;
  });

  // 注册一个每隔5秒循环执行的计时器
  auto loopTimer = yasio_shared_service()->schedule(std::chrono::seconds(5), []()->bool{
    printf("time called!\n");
    return false;
  });


.. _builtin_resolv:

io_service::builtin_resolv
---------------------------
内置域名解析， 会自动判断本地主机ipv6网络环境情况

.. code-block:: cpp

 int builtin_resolv(std::vector<ip::endpoint>& endpoints, const char* hostname,
                                 unsigned short port = 0)

Parameters
>>>>>>>>>>>>>>>>>>
| *endpoints*
| 存储域名解析结果地址列表
| 
| *hostname*
| 域名
| 
| *port*
| 端口

Return Value
>>>>>>>>>>>>>>>>>>
返回0成功， -1失败

.. _channel_at:

io_service::channel_at
----------------------------
根据信道索引获取信道对象

.. code-block:: cpp

 io_channel* channel_at(size_t cindex) const

Parameters
>>>>>>>>>>>>>>>>>>
| *cindex*
| 信道索引


.. _options:

io_service options
-------------------

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 枚举值
     - 参数说明
   * - YOPT_S_DEFERRED_EVENT
     - 设置是否使用事件队列延迟分派网络事件，参数deferred:int，默认值1
   * - YOPT_S_RESOLV_FN
     - | 设置自定义域名解析回调，参数类型resolv_fn_t*
   * - YOPT_S_PRINT_FN
     - 设置打印函数, 参数类型print_fn_t*
   * - YOPT_S_EVENT_CB
     - 设置网络事件回调, 参数类型io_event_cb_t*
   * - YOPT_S_TCP_KEEPALIVE
     - | 设置TCP底层心跳, 参数:
       | idle:int(7200), interal:int(75), probes:int(10)
   * - YOPT_S_NO_NEW_THREAD
     - 设置是否禁用线程，直接在阻塞在start_service调用者线程处理网络事件，参数类型int, 默认值0
   * - YOPT_S_SSL_CACERT
     - 设置ssl客户端证书，参数类型const char*
   * - YOPT_S_CONNECT_TIMEOUT
     - 设置TCP客户端连接超时，参数类型int，默认值10(s)
   * - YOPT_S_DNS_CACHE_TIMEOUT
     - | 设置DNS解析缓存超时时间，参数:L
       | dns_cache_timeout:int 默认值600(s)
   * - YOPT_S_DNS_QUERIES_TIMEOUT
     - | 设置DNS解析超时时间，参数类型:
       | dns_resov_timeout:int，默认值10(s)，仅当启用c-ares异步域名解析时才有效
   * - YOPT_C_LFBFD_FN
     - | 设置信道自定义长度解析函数，用于TCP底层拆包，参数:
       | cindex:int, decode_len_fn_t*
   * - YOPT_C_LFBFD_PARAMS
     - | 设置信道基于netty的LengthBasedFrameDecoder拆包参数，参数:
       | cindex:int, max_frame_length:int, length_field_offset:int, length_field_length:int, length_adjustment:int
   * - YOPT_C_LOCAL_PORT
     - | 指定信道本地绑定端口，参数:
       | cindex:int, port:int(0)
   * - YOPT_C_REMOTE_HOST
     - | 设置信道远程主机ip， 参数:
       | cindex:int, ip:const char*格式为：点分十进制
   * - YOPT_C_REMOTE_PORT
     - | 设置信道远程主机端口， 参数:
       | cindex:int, port:int
   * - YOPT_C_REMOTE_ENDPOINT
     - | 设置信道远程主机ip和端口， 参数:
       | cindex:int, ip:const char*, port:int
   * - YOPT_C_MOD_FLAGS
     - | 修改信道标记为，参数:
       | cindex:int, flagsToAdd:int, flagsToRemove:int
   * - YOPT_C_ENABLE_MCAST
     - | 启用信道组播，参数:
       | cindex:int, multi_addr:const char*, loopback:int  
   * - YOPT_C_DISABLE_MCAST
     - | 禁用信道组播，参数:
       | cindex:int
   * - YOPT_T_CONNECT_UDP
     - | 使用UDP transport和远程地址绑定，一旦绑定，无法解绑，参数:
       | transport:transport_handle_t
   * - YOPT_SOCKOPT
     - | 设置io对象socket选项，参数：
       | obj:io_base*,level:int,optname:int,optval:int,optlen:int
