tracer - event
==============

1 介绍
------

event分为静态和动态两种, 静态event也成为tracepoint, 动态event一般基于kprobe/uprobe.

.. code:: c

   # 查看支持的tracepoints
   cat available_events


