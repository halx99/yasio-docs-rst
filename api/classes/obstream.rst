obstream
^^^^^^^^^^^^^^^^^^
二进制写入流，可以非常方便地进行二进制编码，写入数值类型时自动转换为网络字节序

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
   * - obstream::write_ix
     - 写入7bit Encoded Int变长存储, 函数模板，支持int和int64_t
   * - obstream::write_byte
     - 写入一个字节
   * - obstream::write
     - 函数模板，写入数值类型(bool,char,short,int,long long,float,double等)数据，会自动转化为网络字节序
   * - obstream::write_v
     - 写入字节流数据，会先写入7bit Encoded Int编码的变长长度域
   * - obstream::write_bytes
     - 写入字节流数据，不含长度域
   * - obstream::buffer
     - 获取stream中所有字节流数据
   * - obstream::empty
     - 判断stream是否为空
   * - obstream::length
     - 获取stream总字节数
   * - obstream::data
     - 获取字节数据指针
   * - obstream::pwrite
     - 函数模板，在指定偏移位置写入数值数据，会自动转化为网络字节序
   * - obstream::swrite
     - 静态函数模板，在指定内存地址写入数值数据，会自动转化为网络字节序
   * - obstream::sub
     - 截取子stream，返回新obstream对象
   * - obstream::write_v32
     - 写入字节流数据，使用32bit长度域 (不常用)
   * - obstream::write_v16
     - 写入字节流数据，使用16bit长度域 (不常用)
   * - obstream::write_v8
     - 写入字节流数据，使用8bit长度域 (不常用)
   * - obstream::write_i24
     - 写入24位有符号整数 (不常用)
   * - obstream::write_u24
     - 写入24位无符号整数 (不常用)

Example
--------------------------
.. tabs::
 .. code-tab:: cpp

  using namespace yasio;
  obstream obs;

  // 内存布局|-8bits-|
  obs.write<int8_t>(12);
  
  // 内存布局|-8bits-|-32bits-|
  obs.push32(); // 为长度字段预留4字节空间
  
  // 内存布局|-8bits-|-32bits-|-16bits-|
  obs.write<int16_t>(52013);
  
  // 内存布局|-8bits-|-32bits-|-16bits-|-16bits(length of the string1)-|-88bits(the string1)-|
  obs.write_v16("hello world");

  // 内存布局|-8bits-|-32bits-|-16bits-|-16bits(length of the string1)-|-88bits(the string1)-|-
  // |8bits(length of the string2)-|-88bits(the string2)-|
  obs.write_v("hello world");
  
  // 将整包长度字节数写入最近一次push保留的4字节内存，完成封包，内存布局不变
  obs.pop32(obs.length());
