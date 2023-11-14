控制器驱动
==============

1 重要数据
----------------

1.1 重要文件
****************
============================== ============================= ==============================
drivers/pinctrl/core.c          pinctrl_register()           pinctrl注册
inlcude/linux/pinctrl.h                                      pinctrl驱动注册接口
============================== ============================= ==============================

1.2 重要结构体
****************
============================== ============================= ==============================
pinctrl_desc                   struct pinctrl_ops \*pctlops  pinctrl接口, 驱动人员实现
--                             struct pinmux_ops \*pmxop     mux接口, 驱动人员实现
--                             struct pinconf_ops \*confops  conf接口, 驱动人员实现
============================== ============================= ==============================

2 驱动编写
-----------------

2.1 设备树配置
***************

.. code-block:: c

    iomuxc {                    // 设备树节点
        xxx-evk {
            eth-grp1 {          // 一般会将pin进行分组, 比如eth, i2c, 将他们引脚放到一个组里
                xxx,pins = <    // xxx,pin为驱动要解析的属性
                    reg val     
                    reg val
                    ...
                >;
            }

            i2c-grp2 {
                xxx,pins = <
                    reg val
                    reg val
                    ...
            }

            ...
        };
    };

2.2 驱动程序
***************

   .. code-block:: c

    # 注册驱动
    struct pinctrl_dev *pcdev;
    struct pinctrl_desc *pcd;

    # 设置必须属性
    pcd->name = "xxx";                  // 控制器名字
    pcd->pins = pins;                   // 所有pin的大数组
    pcd->npin = npin;                   // pin数量
    pcd->owner = THIS_MODULE;           // 此模块
    pcd->pctlops = xxx_pctrl_ops;       // *** 驱动实现pctrl ops
    pcd->confops = xxx_pconf_ops;       // *** 驱动实现pconf ops
    pcd->pmxops  = xxx_pmux_ops;        // *** 驱动实现mux ops

    # 解析设备树, 填充priv数据
    parse_dtb();

    # 注册驱动
    pcdev = pinctrl_register(pcd, dev, priv);
