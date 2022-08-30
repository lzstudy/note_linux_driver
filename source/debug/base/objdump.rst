反汇编
======

生成反汇编文件
--------------

.. code:: c

   arm-linux-gnueabi-objdump -S -d -l xxx_module > xxx_module.S

* -d 显示纯汇编文本
* -l 显示行号
* -s 显示所有汇编段 + 源码
* -S 只显示源码

查看模块加载地址
----------------

.. code::c

   $ cat /proc/modules

   ov12895_mipi 8908 0 - Live 0xbf01f000

.. note::

   * 由于内核模块是动态加载的, 当oops后, 地址 = 偏移地址 + 基础地址.
   * cat /proc/modules是查看基本地址, 从而计算出偏移地址

利用反汇编
----------

例如<bf713040>, 死机后会打印出两种信息, 真实地址 和 函数名 + 偏移地址

真实地址
********

需要依赖于cat /proc/modules来计算偏移地址, 然后用addr2line计算

符号信息 + 偏移
***************

例如led_init + 0x40/0x50, 就可以根据反汇编代码, 看到led_init地址, 然后加上0x40判断问题点

