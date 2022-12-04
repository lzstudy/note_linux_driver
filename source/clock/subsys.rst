i2c子系统
==========

1 系统流程
-----------

1.1 系统初始化流程
*******************

1.2 总线初始化流程
*******************

1.3 设备注册流程
*****************

.. code-block:: c

    ############################################# 1 i2c_register_driver
    # 1.1 总线添加驱动
    i2c_register_driver => driver_register => bus_add_driver => driver_probe_device

    # 1.2 驱动匹配
    driver_probe_device => really_probe => i2c_device_probe => teest_probe => printk
