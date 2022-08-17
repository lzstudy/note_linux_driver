同步互斥机制
============

等待队列
--------

.. code:: c

   #include <linux/uaccess.h>

   # 等待队列定义
   wait_queue_head_t wq;

   # 等待队列初始化
   init_waitqueue_head(&wq);

   # 唤醒等待队列
   wake_up_interruptible(&wq);

   # 阻塞等待
   wait_event_interruptible(&wq, cond);

.. warning::

   还没有理解透彻, 比如add_wait_interrupt等接口

信号量
------

.. code:: c

   #include <linux/semaphore.h>

   # 信号量定义
   struct semaphore sem;

   # 信号量初始化
   sema_init(&sem, 0);

   # 信号量释放, 一般再中断中释放
   up(&sem);

   # 等待信号量, 一般再底半部或线程中
   down(&sem);
   down_interruptible(&sem)

.. note::

   down阻塞等待信号量不可中断, 应用层调用后ctrl + C也无法唤醒, 为了解决这个问题产生了down_interruptible

互斥锁
------

用于锁住一部分逻辑, 不会被重入

.. code:: c

   #include <linux/uaccess.h>

   #定义互斥锁
   struct mutex lock;

   # 初始化互斥锁
   mutex_init(&lock);

   # 使用场景
   mutex_lock(&lock);

   ...

   mutex_unlock(&lock);


自旋锁
------

.. code:: c

   # 定义自旋锁
   spinlock_t lock;

   # 初始化自旋锁
   spin_lock_init(&lock);

   # 使用场景 - 保存 + 恢复中断(常用)
   unsigned long flags
   spin_lock_irqsave(&lock, flags);

   ...

   spin_unlock_irqrestore(&lock, flags);

.. note::

   - 自旋锁会禁止处理器抢占
   - 使用spin_lock_irqsave会同时禁止本地中断

