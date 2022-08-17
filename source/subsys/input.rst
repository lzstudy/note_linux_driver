子系统 - INPUT
==============

模板文件
--------

模板使用说明
------------

流程
----


技巧
----

.. code:: c

   # 申请input 设备
   devm_input_allocate_device

   #
   input_set_drvdata(input, ddata);
   input_get_drvdata(input, ddata);
