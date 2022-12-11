工作流程
===========

1 驱动编写流程
----------------------

.. code-block:: c

    # 1 解析设备树
    # 2 初始化摄像头

    # 3 将i2c初始化为subdev, subdev中的ops很重要, 此函数内部只是对SD进行初始化, i2c与client相互指向
    v4l2_i2c_subdev_init(sd, i2c_client, ops);

    # 3 注册media entity, 如果使用media子系统
    media_entity_pads_init(sd->entity, num, &pads);

    # 4 注册subdev, subdev子设备与控制器通常都是分开注册的, 
    # 控制器接收v4l2_async_notifier来匹配子设备, 将其注册到v4l2_device统一管理
    v4l2_async_register_subdev_sensor_common(sd);


.. note::
    
    sudev中的ops是核心代码, 分为三大类, ``core``, ``video``, ``pad``, 每个大类下还有数个接口需要实现


