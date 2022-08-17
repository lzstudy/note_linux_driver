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

   input_set_capability

   驱动中 dev_set_name 来直接修改驱动名称

推荐源码
--------

::
 
   gpio-keys.c
   dump_reg.c
   input.c
