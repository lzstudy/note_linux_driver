工作流程
===========

1 设置某个引脚为指定功能
-------------------------

.. code-block::

    ############################################# 1 设备树部分
    # 1.1 设置引脚配置
	pinctrl_csi_mclk: csi_mclk_grp {
		fsl,pins = <MX8MP_IOMUXC_GPIO1_IO15__CCM_CLKO2	0x59>;
	};

    # 1.2 插入到指定功能
    imx577@1a {
        pinctrl-0 = <&pinctrl_csi0_pwn>, <&pinctrl_csi0_rst>, <&pinctrl_csi_mclk>;
    }
