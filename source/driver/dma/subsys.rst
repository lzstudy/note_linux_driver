DMA子系统
=========

1 基本介绍
----------

1.1 常见术语
************

=== =======================
sg  scatter gather buffers
=== =======================

1.2 工作流程
************

- 申请DMA通道 ``dma_request_chan``
- 配置DMA通道 ``dmaengine_slave_config``
- 使能DMA传输 ``dmaengine_prep_slave_sg``
- 提交DMA传输 ``dmaengine_submit``
- 启动DMA传输 ``dma_async_issue_pending``


.. note::

   - ``dmaengine_prep_dma_cyclic`` 用于音频场景, 每传输一段就会产生DMA中断
   - ``dmaengine_prep_interleaved_dma`` 用于图像显示
