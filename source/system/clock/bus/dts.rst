设备树
=========

1 语法说明
*************

============= ==== ===============================================================
属性名        必须 说明
#clock-cell   Y    若属性值为0, 表示就提供一个时钟, 若属性值为1, 表示提供多个时钟
============= ==== ===============================================================

1.2 例程分析
*************

.. code-block:: dts

    # 24M晶振 - fixed-clock
	osc_24m: clock-osc-24m {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <24000000>;
		clock-output-names = "osc_24m";
	};

    # 锁相环
    clk: clock-controller@30380000 {
        compatible = "fsl,imx8mp-ccm";
        reg = <0x30380000 0x10000>;
        #clock-cells = <1>;
        clocks = <&osc_32k>, <&osc_24m>, <&clk_ext1>, <&clk_ext2>,
                <&clk_ext3>, <&clk_ext4>;
        clock-names = "osc_32k", "osc_24m", "clk_ext1", "clk_ext2",
                    "clk_ext3", "clk_ext4";
        ···



2 consumer
--------------

2.1 基本说明
*************

2.2 例程分析
*************

3 综合例程
-----------

这里以开启nxp的i2c时钟为例

.. code-block:: dts

    # 24M晶振 - fixed-clock
	osc_24m: clock-osc-24m {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <24000000>;
		clock-output-names = "osc_24m";
	};

    # 锁相环
    clk: clock-controller@30380000 {
        compatible = "fsl,imx8mp-ccm";
        reg = <0x30380000 0x10000>;
        #clock-cells = <1>;
        clocks = <&osc_32k>, <&osc_24m>, <&clk_ext1>, <&clk_ext2>,
                <&clk_ext3>, <&clk_ext4>;
        clock-names = "osc_32k", "osc_24m", "clk_ext1", "clk_ext2",
                    "clk_ext3", "clk_ext4";
        ···

    # i2c时钟
    i2c2: i2c@30a30000 {
        #address-cells = <1>;
        #size-cells = <0>;
        compatible = "fsl,imx8mp-i2c", "fsl,imx21-i2c";
        reg = <0x30a30000 0x10000>;
        interrupts = <GIC_SPI 36 IRQ_TYPE_LEVEL_HIGH>;
        clocks = <&clk IMX8MP_CLK_I2C2_ROOT>;
        status = "disabled";
    };


.. note::

    i2c2使用clk时钟树中 ID为IMX8MP_CLK_I2C2_ROOT的时钟, IMX8MP_CLK_I2C2_ROOT的值在驱动中可以找到, 这里为206





