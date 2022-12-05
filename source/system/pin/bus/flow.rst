工作流程
=========


1 时钟驱动注册流程
-----------------------

================================================== ====================================
kernel/drivers/pinctrl/freescale/pinctrl-imx8mp.c  pinctrl驱动
================================================== ====================================

.. code-block::

    ############################################# 1 设备树部分
    # 1.1 IO复用节点
    iomuxc: pinctrl@30330000 {
        compatible = "fsl,imx8mp-iomuxc";
        reg = <0x30330000 0x10000>;
    };

    ############################################# 2 驱动部分
    # 2.1 imx_pinctrl_probe
    imx_clk_mux2 + imx_clk_gate3 + imx_clk_divider2 + imx8m_clk_composite + of_clk_add_provider

    # 2.2 of_clk_add_provider

