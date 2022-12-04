gpio语法
==========

1 平台节点
-------------

- 参考fsl-imx-gpio.txt
- 这里的一个gpio指的是gpio端口, 既一组gpio

.. code-block:: dts

    gpio2: gpio@30210000 {
        compatible = "fsl,imx8mp-gpio", "fsl,imx35-gpio";
        reg = <0x30210000 0x10000>;
        interrupts = <GIC_SPI 66 IRQ_TYPE_LEVEL_HIGH>,
                    <GIC_SPI 67 IRQ_TYPE_LEVEL_HIGH>;
        clocks = <&clk IMX8MP_CLK_GPIO2_ROOT>;
        gpio-controller;
        #gpio-cells = <2>;
        interrupt-controller;
        #interrupt-cells = <2>;
    };

2 用户节点
-------------

.. code-block:: dts

    t1-gpios = <&gpio2 11 GPIO_ACTIVE_HIGH>;
    t2-gpios = <&gpio1 6 GPIO_ACTIVE_LOW>;

3 节点解析
-------------

2.2 of_get_named_gpio解析
**************************

.. code-block:: c

    # 函数原型
    int of_get_named_gpio(struct device_node *np, const char *propname, int index)

    # 使用例子
    int pin = of_get_named_gpio(dev->of_node, "t1-gpios", 0);


