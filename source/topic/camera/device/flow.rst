工作流程
===========

1 驱动编写流程
----------------------

.. code-block:: c

    ############################################ 1 解析设备树, 复位引脚、电源引脚、时钟源
    hw->pin_power = of_get_named_gpio(dev->of_node, "pwn-gpios", 0);
    devm_gpio_request_one(dev, hw->pin_power, GPIOF_OUT_INIT_HIGH, "imx577-power");

    hw->pin_reset = of_get_named_gpio(dev->of_node, "rst-gpios", 0);
    devm_gpio_request_one(dev, hw->pin_reset, GPIOF_OUT_INIT_HIGH, "imx577-reset");

    hw->clk = devm_clk_get(dev, "csi_mclk");

    ############################################ 2 初始化摄像头, 设置时钟频率、上电、复位
    clk_set_rate(hw->clk, 24000000);
    clk_prepare_enable(hw->clk);

    imx577_set_power(hw->pin_power, 1);
    imx577_reset(hw->pin_reset);

    ############################################ 3 注册subdev, subdev中的ops是工作核心
    ## 3.1 初始化PADS, sensor的pad一般只有1个, 且只能为SORCE
    struct media_pad *pads = devm_kzalloc(&client->dev, sizeof(struct media_pad) * 1, GFP_KERNEL);
    pads[0].flags = MEDIA_PAD_FL_SOURCE;

    ## 3.2 初始化media, 其中media_ops一般什么也不做, 但不能为空
    v4l2_i2c_subdev_init(sd, client, &imx577_subdev_ops);
    sd->flags |= V4L2_SUBDEV_FL_HAS_DEVNODE;
    sd->entity.function = MEDIA_ENT_F_CAM_SENSOR;
    sd->entity.ops = &imx577_sd_media_ops;

    # 3.3 关联media和pad
    media_entity_pads_init(&sd->entity, 1, pads);

    ## 3.4 注册subdev
    v4l2_async_register_subdev_sensor_common(sd);


.. note:: 
    
    v4l2_async_register_subdev_sensor_common, 控制器接收v4l2_async_notifier来匹配子设备, 将其注册到v4l2_device统一管理


.. note::
    
    sudev中的ops是核心代码, 分为三大类, ``core``, ``video``, ``pad``, 每个大类下还有数个接口需要实现


