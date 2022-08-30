栈回溯
======

说明
----

模块死机想要栈回溯, 需要知道模块加载地址, 可以通过 ``cat /proc/module`` 来查看. 如果是在module_init时崩溃, 那么需要插入内核探针, 在kprobe时查看加载地址


使用方法
--------

.. code::c 

   arm-linux-addr2line -C -f -e xxx.ko <reg0> <reg1> <reg2>
