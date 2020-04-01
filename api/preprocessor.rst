yasio 预处理器定义
^^^^^^^^^^^^^^^^^^

以下宏定义可以控制yasio的行为, 可以再 `yasio/detail/config.hpp <https://github.com/yasio/yasio/blob/master/yasio/detail/config.hpp>`_ 更改或者定义为编译器宏

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 宏定义
     - 说明
   * - YASIO_HEADER_ONLY
     - 是否以仅仅头文件的方式使用yasio核心组件，默认关闭
   * - YASIO_VERBOSE_LOG，默认关闭
     - 是否打印详细日志
   * - YASIO_DISABLE_SPSC_QUEUE
     - 是否禁用SPSC队列，在非单线程发送数据情况先需要禁用，默认启用
   * - YASIO_DISABLE_OBJECT_POOL 
     - 是否禁用对象池的使用，默认启用
   * - YASIO_ENABLE_ARES_PROFILER 
     - 是否启用异步域名解析性能日志，默认关闭
   * - YASIO_HAVE_CARES
     - 是否启用c-ares异步域名解析库
   * - YASIO_HAVE_KCP
     - 是否启用kcp传输支持
   * - YASIO_HAVE_SSL 
     - 是否启用ssl客户端支持
   * - YASIO_DISABLE_CONCURRENT_SINGLETON 
     - 是否禁用并发单利类模板
   