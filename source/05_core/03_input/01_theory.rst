基础理论
===========

=========================== ==========================
driver/input/input.c         input核心文件
include/uapi/linux/input.h   输入事件类型定义
=========================== ==========================

调试
--------------

.. code-block:: c

    hexdump /dev/input/eventx
    od -x /dev/input/eventx
