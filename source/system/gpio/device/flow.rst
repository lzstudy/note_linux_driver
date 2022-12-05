工作流程
===========

1 获取GPIO并使用流程
----------------------

.. code-block:: c

    ############################################# 1 设备树部分
    # 1.1 配置clocks节点, 其中CLK_I2C_ROOT是时钟唯一ID, 由厂商定义, 每个时钟都会有唯一一个ID
    clocks = <&clk CLK_I2C_ROOT>

    # 1.2 配置clock-names节点, 与clocks节点配合使用, 驱动中通过此标识获取到clocks
    clock-names = "zw-i2c-clk"

    ############################################# 2 驱动部分
    #include <linux/clk.h>

    # 2.1 解析设备树获取pin
    int pin = of_get_named_gpio(dev->of_node, "t1-gpios", 0);

    # 2.2 配置接口(申请并设置)
    devm_gpio_request_one(dev, pin, GPIOF_OUT_INIT_HIGH, "gpio-lable");

    # 2.3 设置接口
    gpio_set_value(pin, 1);


