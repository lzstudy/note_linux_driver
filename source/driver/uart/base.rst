uart驱动
========

1 介绍
------

串口框架包括三部分：tty、serial、具体驱动

1.1 参考资料
------------

``内核文档``
    Document/serial/

2 tty框架
---------

2.1 相关文件
************

============= ==============
tty_port.c
tty_io.c
tty_buffer.c
tty_ldisc.c   
============= ==============

2.2 tty初始化流程
*****************

.. code:: c

   chr_dev_init (drivers/char/mem.c)
       tty_init (drivers/tty/io.c)
           cdev_add(&tty_cdev, &tty_fops) (drivers/tty/io.c)

.. note::

   此过程知识注册了/dev/tty设备, 该设备主设备号为5

3. 系统调用流程
---------------

3.1 open
********


