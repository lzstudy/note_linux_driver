综合例程
============

1. 例程描述
--------------

这里以imx577 sensor为例, imx577使用i2c接口进行控制, 通过mipi进行传输图像数据.
所以涉及i2c时钟的开启与使用, 还有imx577的外部时钟提供


1. 设备树
---------------

.. code-block:: dts

    # 系统24M晶振节点
	osc_24m: clock-osc-24m {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <24000000>;
		clock-output-names = "osc_24m";
	};

    # 时钟控制器节点
    clk: clock-controller@30380000 {
        compatible = "fsl,imx8mp-ccm";
        reg = <0x30380000 0x10000>;
        #clock-cells = <1>;
        clocks = <&osc_32k>, <&osc_24m>, <&clk_ext1>, <&clk_ext2>,
                <&clk_ext3>, <&clk_ext4>;
        clock-names = "osc_32k", "osc_24m", "clk_ext1", "clk_ext2",
                    "clk_ext3", "clk_ext4";
        ...
    };

    # i2c节点
    i2c2: i2c@30a30000 {
        #address-cells = <1>;
        #size-cells = <0>;
        compatible = "fsl,imx8mp-i2c", "fsl,imx21-i2c";
        reg = <0x30a30000 0x10000>;
        interrupts = <GIC_SPI 36 IRQ_TYPE_LEVEL_HIGH>;
        clocks = <&clk IMX8MP_CLK_I2C2_ROOT>;
        status = "disabled";
    };

    # imx577节点
    &i2c2 {
        clock-frequency = <100000>;
        pinctrl-names = "default";
        pinctrl-0 = <&pinctrl_i2c2>;
        status = "okay";

        imx577: imx577@1a {
            compatible = "ovti,ov2775", "sony,imx577", "basler,basler-camera-vvcam", "basler-camera-vvcam";
            pinctrl-0 = <&pinctrl_csi0_pwn>, <&pinctrl_csi0_rst>, <&pinctrl_csi_mclk>;
            pinctrl-names = "default";
            reg = <0x1a>;
            csi_id = <0x00>;
            status = "okay";
            clocks = <&clk IMX8MP_CLK_IPP_DO_CLKO2>;
            clock-names = "csi_mclk";
            assigned-clocks = <&clk IMX8MP_CLK_IPP_DO_CLKO2>;
            assigned-clock-parents = <&clk IMX8MP_CLK_24M>;
            assigned-clock-rates = <24000000>;
            pwn-gpios = <&gpio2 11 GPIO_ACTIVE_HIGH>;
            rst-gpios = <&gpio1 6 GPIO_ACTIVE_LOW>;
            mclk = <24000000>;
            mclk_source = <0>;
        };
    };