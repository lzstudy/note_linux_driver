工作流程
=========


1 CSI驱动
-----------------------

.. code-block:: c

    ############################################# 1 probe
    # 1.1 私有数据 + 解析设备树 + 硬件初始化
    devm_kzalloc + mipi_csis_parse_dt + mipi_csis_phy_init

    # 1.2 申请中断 + 使能时钟
    platform_get_irq + mipi_csis_clk_get + mipi_csis_irq_handler

    # 1.3 初始化subdev + media entity
    # 对于4个lane, 一般pad有8个, 4个source, 4个sink
    mipi_csis_subdev_init(sd, pdev, ops) + media_entity_pads_init

    ############################################# 2 中断处理 mipi_csis_irq_handler

2 DWE驱动
------------

dwe功能说明

- YUV422和YUV420输入和输出
- 

.. code-block:: c

    ############################################# 1 probe
    # 1.1 私有数据 + 设备树解析 + 硬件初始化
    devm_kzalloc + devm_clk_get

    # 1.2 注册子设备, 其中ops的open有关于中断初始化
    v4l2_subdev_init(&dwe_dev->sd, &dwe_v4l2_subdev_ops);  => dwe_open => devm_request_irq(dwe_hw_isr)
    v4l2_device_register + v4l2_device_register_subdev

    # 1.3 注册subdev_node
    v4l2_device_register_subdev_nodes

    ############################################# 2 dwe_hw_isr 中断, 关于如何读取摄像头数据
    # 2.1 如果收到一帧完成状态, 则更新dma buffer
    status & INT_FRAME_DONE + vvbuf_ready + update_dma_buffer

    ############################################# 3 vvbuf_ready

    ############################################# 4 update_dma_buffer 中断, 关于如何读取摄像头数据
    # 4.1 设置DMA 基本参数
    dwe_s_params

    # 4.2 设置dmabuffer参数, 主要设置DMA目标地址
    dwe_set_buffer => set(DST_IMG_Y_BASE) + set(DST_IMG_UV_BASE)            // 设置plane Y和UV的dmadst地址

    # 4.3 启动DMAR read, 主要设置DMA源地址
    dwe_start_dma_read => set(SRC_IMG_Y_BASE) + set(SRC_IMG_UV_BASE)

    # 4.4 使能DMA
    dwe_enable_bus()


