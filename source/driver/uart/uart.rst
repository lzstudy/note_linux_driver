串口驱动介绍
============


调用流程
--------

.. code:: c

   tty_write (tty_io.c)
      uart_write (serial_core.c)
        startup (imx.c - 具体平台)
