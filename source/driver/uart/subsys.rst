串口子系统
==========

1. 基本介绍
-----------

1.1 常见术语
************

1.2 重要文件
************

============== ======================================
文件           描述
n_tty.c        线路规程文件, 串口最上层文件与应用对接
tty_core.c     tty架构文件
serial_core.c  串口架构文件
imx.c          串口驱动文件
============== ======================================

============== ======================================
文件           描述
tty_io.c       open
tty_buffer.c   dma缓冲区管理
tty_port.c     串口端口管理
tty_ldsic.c    线路规程
============== ======================================

1.3 工作流程
************

- 申请DMA通道(内存和外设建立连接)
- DMA请求(例如串口接收FIFO达到阈值)

1.4 常用参数
************

2. 源码分析
-----------

2.1 调用流程
************

.. code::

   # 串口初始化
   open -> uart_open -> imx_uart_startup                        [imx.c]
     imx_uart_dma_init -> dmaengine_slave_config                [配置DMA通道, imx_uart_dma_init]
     imx_uart_start_rx_dma
	 sg_init_one(sgl,rx_buf,PAGE_SIZE]                          // 申请DMA缓冲区为PAGE_SIZE
	 dma_map_sg
	 desc->callback = imx_uart_dma_rx_callback                  // DMA接收中断
	 dmaengine_prep_dma_cyclic                                  // 采用cyclic方式
	 dmaengine_submit

   # 串口接收, 采用ring接收
   imx_uart_dma_rx_callback -> tty_flip_buffer_push             [tty_buffer.c]
     queue_work(q, flush_to_ldisc)                              [tty_buffer.c tty_buffer_init实现]
	   flush_to_ldisc -> receive_buf -> port->client_ops->receive_buf
	     tty_port_default_receive_buf                           [tty_port.c]
		   tty_ldisc_receive_buf -> ld->ops->receive_buf        [tty_buffer.c, tty_reigster_ldisc注册ops]
		     n_tty_receive_buf -> n_tty_receive_buf_common      [n_tty.c]
			   __receive_buf -> n_tty_receive_buf_standard      [n_tty.c]
			     kill_fasync                                    [发送信号通知应用]
				 wake_up_interruptible_poll                     [唤醒read和poll的阻塞]
		     
		     













