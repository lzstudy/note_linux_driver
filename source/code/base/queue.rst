内核中的队列
============

循环队列
--------

.. code:: c

   #include <linux/kfifo.h>

   # 定义循环队列
   struct __kfifo fifo;

   # 队列初始化
   __kfifo_alloc(&fifo, sizeof(priv->ltsw->id) * 2, sizeof(int), GFP_KERNEL);

   # 队列输入
   __kfifo_in(&fifo, &id, sizeof(id));

   # 队列输出
   __kfifo_out(&fifo, &id, sizeof(id));

   # 释放循环队列
   __kfifo_free(&fifo);
