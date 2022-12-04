gpio接口
===========

1 重要文件
-------------

kernel/include/linux/gpio.h

2 接口使用
-------------

2.1 接口方案一
****************

.. code-block:: c

    # 1 解析设备树获取pin
    int pin = of_get_named_gpio(dev->of_node, "t1-gpios", 0);

    # 2 配置接口(申请并设置)
    devm_gpio_request_one(dev, pin, GPIOF_OUT_INIT_HIGH, "gpio-lable");

    # 3 设置接口
    gpio_set_value(pin, 1);


.. note:: 
    
    对于有些挂载在I2C, SPI总线上的扩展GPIO, 读写操作可能会导致睡眠, 因此不能在中断函数中使用。
    使用下面的函数来区别于正常的GPIO, ``gpio_set_value_cansleep``. 比如使用i2c总线扩展了gpio
    此时控制GPIO需要先通过i2c总线, 此行为就有可能导致睡眠
