时间接口
========

非睡眠延时函数
--------------

.. code:: c

   #include <linux/delay.h>

   # ns延时
   ndelay(100);

   # us延时
   udelay(100);

   # ms延时
   mdelay(100);

.. note::

   内核中的延时本质上是忙等待, 不会导致线程切换

睡眠延时
--------

.. code:: c

   # 休眠s
   ssleep(100)

   # 休眠ms
   msleep(100)

   # 休眠ms可被ctrl + c打断
   msleep_interruptible(100)



