clock接口
===========

CCF框架
https://zhuanlan.zhihu.com/p/558783244

1 使用流程
-----------

.. code-block:: c

    # 1 获取时钟
    struct clk *clock = devm_clk_get(dev, "csi_mclk");

    # 2 设置频率
    clk_set_rate(clock, 240000);

    # 3 使能时钟(clk_prepare + clk_enable)
    clk_prepare_enable(clock);

2 接口说明
----------

2.1 获取时钟的接口
*********************

.. code-block:: c

    # 方法1
    devm_clk_get

    # 方法2
    clk_get

    # 方法3
    of_clk_get

2.2 时钟使能接口
******************

.. code-block:: c

    # 方法1
    clk_prepare_enable(clk);

    # 方法2
    clk_prepare
    clk_enable


N 说明
-------

为什么引入clk_prepare, 在过去内核中没有prepare之说的, 直接enable或disable, 后来有些
硬件的enable/disable会引起睡眠, 从而导致enable和diable接口不能再原子上下文进行. 所以
引入prepare, 把enable/diable差分中不可再原子上下文调用的(prepare接口), 和可以再原子
上下文调用的(enable/disable)接口