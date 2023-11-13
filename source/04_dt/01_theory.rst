基础理论
===========

overview
--------------

    .. image:: _images/dt.png

    设备树的属性有标准属性和自定义属性两种。

1 术语
----------------

============== ================================ ==========================
DTB             device tree blob                设备树对象
DTS             device tree syntax              设备树语法
SMP             symmetric multiprocessing       对称多核处理器
BLOB            binary larger object            二进制大对象
============== ================================ ==========================

2 设备树调试
-----------------

    内核运行后, 可以到 ``/proc/device-tree`` 下查看, 从而检查设备节点是否成功解析

.. code-block:: c

    # 查看设备树节点, 以下两个目录相同
    cd /proc/device-tree
    cd /sys/firmware/devicetree/base
