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


高精度定时器
------------

.. code:: c

   #include <linux/hrtimer.h>

   # 定义结构
   struct hrtimer timer;

   # 中断函数
   enum hrtimer_restart timer_isr(struct hrtimer *timer)
   {
       # 不重启定时器
       return HRTIMER_NORESTART;

       # 重启定时器
       hrtimer_forward_now(timer, ns_to_ktime(1000);
       return HRTIMER_RESTART;
   }

   # 初始化定时器
   hrtimer_init(&timer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
   timer.function = timer_isr;

   # 取消定时器
   hrtimer_cancle(&timer);


普通定时器
----------

.. code:: c

   #include <linux/uaccess.h>

   # 定义定时器
   struct timer_list timer;

   # 定时器中断处理函数
   static void timer_isr(unsigned long _data)
   {
   }

   # 按键中断处理函数, 再这里启动定时器
   static irqreturn_t gpio_keys_irq_isr(int irq, void *dev_id)
   {
       # 设置定时器启动
       mod_timer(&timer, jiffies + msecs_to_jiffies(10));
       return IRQ_HANDLED;
   }

   # 初始化定时器
   setup_timer(&timer, timer_isr, (unsigned long)context);


   # 删除定时器
   del_timer_sync(&timer);





