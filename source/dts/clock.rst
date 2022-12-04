clock语法
===========

cat /sys/kernel/debug/clk/clk_summary 查看时钟信息

1 平台节点
-------------

.. code-block:: dts

	osc_32k: clock-osc-32k {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <32768>;
		clock-output-names = "osc_32k";
	};

	osc_24m: clock-osc-24m {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <24000000>;
		clock-output-names = "osc_24m";
	};

	clk_ext1: clock-ext1 {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <133000000>;
		clock-output-names = "clk_ext1";
	};

	clk_ext2: clock-ext2 {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <133000000>;
		clock-output-names = "clk_ext2";
	};

	clk_ext3: clock-ext3 {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <133000000>;
		clock-output-names = "clk_ext3";
	};

	clk_ext4: clock-ext4 {
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency= <133000000>;
		clock-output-names = "clk_ext4";
	};

    clk: clock-controller@30380000 {
        compatible = "fsl,imx8mp-ccm";
        reg = <0x30380000 0x10000>;
        #clock-cells = <1>;
        clocks = <&osc_32k>, <&osc_24m>, <&clk_ext1>, <&clk_ext2>,
                <&clk_ext3>, <&clk_ext4>;
        clock-names = "osc_32k", "osc_24m", "clk_ext1", "clk_ext2",
                    "clk_ext3", "clk_ext4";
        assigned-clocks = <&clk IMX8MP_CLK_NOC>,
                    <&clk IMX8MP_CLK_NOC_IO>,
                    <&clk IMX8MP_CLK_GIC>,
                    <&clk IMX8MP_CLK_AUDIO_AHB>,
                    <&clk IMX8MP_CLK_AUDIO_AXI_SRC>,
                    <&clk IMX8MP_CLK_IPG_AUDIO_ROOT>,
                    <&clk IMX8MP_AUDIO_PLL1>,
                    <&clk IMX8MP_AUDIO_PLL2>,
                    <&clk IMX8MP_VIDEO_PLL1>;
        assigned-clock-parents = <&clk IMX8MP_SYS_PLL2_1000M>,
                        <&clk IMX8MP_SYS_PLL1_800M>,
                        <&clk IMX8MP_SYS_PLL2_500M>,
                        <&clk IMX8MP_SYS_PLL1_800M>,
                        <&clk IMX8MP_SYS_PLL1_800M>;
        assigned-clock-rates = <1000000000>,
                        <800000000>,
                        <500000000>,
                        <400000000>,
                        <800000000>,
                        <400000000>,
                        <393216000>,
                        <361267200>,
                        <1008000000>;
    };