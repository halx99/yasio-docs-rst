obstream
============================================
二进制写入流，写入数值类型时自动转换为网络字节序

命名空间
---------------------
``namespace yasio``

成员
-----------------
.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - obstream::obstream
     - 构造obstream对象

公共方法:

.. list-table:: 
   :widths: auto
   :header-rows: 1

   * - 函数名
     - 函数说明
   * - obstream::write_i
     - 函数模板，写入数值数据，会自动转化为网络字节序
   * - obstream::write_i24
     - 写入24位有符号整数
   * - obstream::write_u24
     - 写入24位无符号证书
   * - obstream::write_i7
     - 写入7bit Encoded Int变长存储
   * - obstream::write_va
     - 写入字节流数据，会先写入7bit编码的变长长度域
   * - obstream::write_v
     - 写入字节流数据，使用32bit长度域
   * - obstream::write_v16
     - 写入字节流数据，使用16bit长度域
   * - obstream::write_v8
     - 写入字节流数据，使用8bit长度域
   * - obstream::write_byte
     - 写入一个字节
   * - obstream::write_bytes
     - 写入字节流数据，不含长度域
   * - obstream::buffer
     - 获取stream中所有字节流数据
   * - obstream::empty
     - 判断stream是否为空
   * - obstream::length
     - 获取stream中长度
   * - obstream::data
     - 获取字节数据指针
   * - obstream::pwrite_i
     - 在指定偏移位置写入数值数据，会自动转化为网络字节序
   * - obstream::swrite_i
     - 静态方法，在指定地址写入数值数据，会自动转化为网络字节序
   * - obstream::sub
     - 截取子stream，返回新obstream对象
