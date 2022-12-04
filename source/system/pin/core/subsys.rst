子系统
===========

1 基本介绍
------------

linux时钟体系由三部分构成 ``common clock framework``, ``clock provider``, ``clock consumer``。

.. image:: clock.jpg


2 框架介绍
------------

2.1 common clock framework
****************************

2.2 clock provider
*********************

根据时钟特点, ccf将时钟抽象为 ``fixed rate``, ``gate``, ``devider``, ``mux``, ``fixed factor``, ``composite``六类
使用数据结构clk_hw来描述

.. code-block:: c

    struct clk_hw {
        struct clk_core *core;
        struct clk *clk;
        const struct clk_init_data *init;
    };

    # fixed rate表示
    struct clk_fixed_rate {
        struct		clk_hw hw;
        unsigned long	fixed_rate;
        unsigned long	fixed_accuracy;
    };

    # gate表示
    struct clk_gate {
        struct clk_hw hw;
        void __iomem	*reg;
        u8		bit_idx;
        u8		flags;
        spinlock_t	*lock;
    };

    ···
    

2.3 clock consumer
*********************
