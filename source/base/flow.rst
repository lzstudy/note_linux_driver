编写流程
===========

1. 驱动匹配
--------------

1.1 平台框架
*************

先编写一个框架, 编译后 ``insmod`` 到内核, ``/sys/bus/xxx/drivers/`` 找到该目录表示成功

.. code-block:: c

    #include <linux/i2c.h>
    #include <linux/module.h>

    static struct i2c_driver test_i2c_driver = {
        .driver = {
            .owner = THIS_MODULE,
            .name  = "zw-test",
            .pm = NULL,
            .of_match_table = NULL,         // 对应设备树匹配
        },
        .probe = NULL,
        .remove = NULL,
        .id_table = NULL,                   // 对应以前的匹配, 所以可以不写此项
    };

    module_i2c_driver(test_i2c_driver);
    MODULE_DESCRIPTION("test i2c Driver");
    MODULE_LICENSE("GPL");

1.2 编写匹配表
**************

子系统中的driver结构的 ``of_match_table`` 用设备树匹配

.. code-block:: c
    
    static const struct of_device_id test_dt_ids[] = {
        {.compatible = "test-compat"},
        {}
    };



2. 编写porbe和remove函数
-------------------------

