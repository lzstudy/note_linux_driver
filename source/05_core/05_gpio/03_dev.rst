外设驱动
============

1 设备树
----------------
.. code-block:: c

    # 1 引用单GPIO
    xxx-gpio = <&gpio1 3 GPIO_ACTIVE_LOW>;

    # 2 引用多GPIO, 下面引用了4个gpio, 其中0表示空的
    xxx-gpios = <0
                &gpio1 1 2
                &gpio2 3 4
                &gpio3 4 5>

.. tip:: 
    
    在引用gpio时, 一定要全局搜索一下有没有其他节点引用了此gpio, 
    然后屏蔽掉相关代码, 防止冲突。


2 设备树解析接口
-------------------
.. code-block:: c

    # 获取GPIO数量, 例如上面案例2就会返回4
    int of_gpio_named_count(np, propname);

    # 返回固定属性gpios的数量
    int of_gpio_count(np);

    # 返回gpio_num, 驱动接口是靠编号来操作的
    int of_get_named_gpio(np, propname, 0);

3 驱动控制接口
--------------------
.. code-block:: c

    # 1 申请gpio
    devm_gpio_request(dev, gpio_num, label);

    # 2 设置输出 + 默认值
    gpio_direction_output(gpio_num, 1/0);
    gpio_set_value(gpio_num, 1/0);

    # 3 设置输入
    gpio_direction_input(gpio_num);
    val = gpio_get_value(gpio_num);

4 GPIO调试
-----------------

4.1 编写程序前调试
************************

.. code-block:: c

    # 进入gpio目录
    cd /sys/class/gpio

    # 导出引脚
    echo 3 > export

    # 设置引脚输出模式
    cd gpio3/
    echo out > direction

    # 设置引脚电平
    echo 1/0 > value
