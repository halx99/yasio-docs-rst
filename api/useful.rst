yasio 常用接口
====================

yasio::highp_clock
------------------
函数模板，获取微秒级稳定时钟或者系统时钟。

.. code-block:: cpp

 template <typename _Ty> 
 inline long long highp_clock();

Example
^^^^^^^^^^^^^^^^^^
.. tabs::
 .. code-tab:: cpp

  long long t1 = yasio::highp_clock<yasio::steady_clock_t>(); // 获取微秒级CPU时钟
  long long t2 = yasio::highp_clock<yasio::system_clock_t>(); // 获取微秒级UTC时间

yasio::xhighp_clock
------------------
函数模板，获取纳秒级稳定时钟或者系统时钟。

.. code-block:: cpp

 template <typename _Ty> 
 inline long long xhighp_clock();

Example
^^^^^^^^^^^^^^^^^^
.. tabs::
 .. code-tab:: cpp

  long long t1 = yasio::xhighp_clock<yasio::steady_clock_t>(); // 获取纳秒级CPU时钟
  long long t2 = yasio::xhighp_clock<yasio::system_clock_t>(); // 获取纳秒级UTC时间

