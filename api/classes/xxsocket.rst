=====================
xxsocket
=====================
xxsocket是跨平台socket工具类， 屏蔽了各操作系统平台差异，提供统一接口，是yasio网络库的起源, 支持C++11 move语义。

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
   * - xxsocket::xxsocket
     - 构造xxsocket对象

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - xxsocket::xpconnect
     - 发起TCP连接，支持本地ipv4/ipv6检测
   * - xxsocket::xpconnect_n
     - 发起TCP连接，支持本地ipv4/ipv6检测, 支持超时
   * - xxsocket::pconnect
     - 发起TCP连接
   * - xxsocket::pconnect_n
     - 发起TCP连接, 支持超时
   * - xxsocket::pserv
     - 启动TCP服务器
   * - io_service::swap
     - 交换socket文件描述符
   * - xxsocket::open
     - 打开套接字
   * - xxsocket::reopen
     - 重新打开套接字
   * - xxsocket::is_open
     - 判断套接字是否已打开
   * - xxsocket::native_handle
     - 返回内部xxsocket管理的socket文件描述符
   * - xxsocket::detach
     - 释放对套接字的所有权
   * - xxsocket::set_nonblocking
     - 设置socket是否为非阻塞状态
   * - xxsocket::test_nonblocking
     - 检测socket是否是非阻塞状态（不可靠的）
   * - xxsocket::bind
     - 绑定套接字，指定地址
   * - xxsocket::bind_any
     - 绑定套接字, 任意地址
   * - xxsocket::listen
     - 启动TCP服务监听
   * - xxsocket::accept
     - 接受一个客户端连接，必须listen之后才可以调用此接口
   * - xxsocket::accept_n
     - 接受一个客户端连接，必须listen之后才可以调用此接口, 支持超时
   * - xxsocket::connect
     - 发起TCP连接, 和pconnect,xpconnect不同的是，套接字必须是已打开状态
   * - xxsocket::connect_n
     - 和connect相同，支持超时
   * - xxsocket::send
     - 发送数据
   * - xxsocket::send_n
     - 发送数据，支持超时
   * - xxsocket::recv
     - 从内核读取对端发来的数据
   * - xxsocket:recv_n
     - 从内核读取对端发来的数据，支持超时
   * - xxsocket:sendto
     - 发送UDP包到指定地址
   * - xxsocket:recvfrom
     - 从内核读取发送到已绑定本地端口的UDP套接字数据
   * - xxsocket::handle_write_ready
     - 等待socket可写，只有当发送缓冲区满了的时候，才会阻塞
   * - xxsocket::handle_read_ready
     - 等待socket可读唤醒
   * - xxsocket::local_endpoint
     - 获取本地地址，socket必须已经成功connect，才能正确返回
   * - xxsocket::peer_endpoint
     - 获取对端地址，socket必须已经成功connect，才能正确返回
   * - xxsocket::set_keepalive
     - 设置TCP协议心跳参数
   * - xxsocket::reuse_address
     - 设置socket是否重用地址/端口
   * - xxsocket::set_optval
     - 设置socket选项，函数模板封装setsockopt，会自动计算大小
   * - xxsocket::get_optval
     - 获取socket选项，函数模板封装getsockopt，会自动计算大小
   * - xxsocket::select
     - 封装bsd socket.select， 使用略有差异
   * - xxsocket::alive
     - 判断socket是否处于正常状态
   * - xxsocket::shutdown
     - 封装bsd socket.shutdown
   * - xxsocket::close
     - 关闭套接字，回收套接字资源
   * - xxsocket::get_last_errno
     - 获取最近socket错误码
   * - xxsocket::set_last_errno
     - 设置最近socket错误码
   * - xxsocket::strerror
     - 获取socket错误码的文本描述信息
   * - xxsocket::gai_strerror
     - 获取getaddrinfo错误码的文本描述信息
   * - xxsocket::resolve
     - 静态方法，域名解析, 解析所有地址
   * - xxsocket::resolve_v4
     - 静态方法，域名解析, 解析ipv4地址
   * - xxsocket::resolve_v6
     - 静态方法，域名解析, 解析ipv6地址
   * - xxsocket::resolve_v4to6
     - 静态方法，域名解析, 只解析ipv4地址，并根据V4MAPPED规则转换为ipv6地址
   * - xxsocket::resolve_tov6
     - 静态方法，域名解析, 解析所有地址，但ipv4地址会根据V4MAPPED规则转换为ipv6地址
