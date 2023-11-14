语法说明
===========

1 头文件引用
-----------------

.. code-block:: c

    # 设备树可以包含3种头文件
    #include <input.h>
    #include "zw.dtsi"
    #include "zw.dts"


2 标准属性
-----------------

    设备树节点有标准节点, 标准节点又存有标准属性, 属性格式 ``name@address``

===================== ========================== ========================================================
属性                  属性值                      说明
alias                 node                       为节点起别名
compatible            string                     内核通过此属性匹配驱动程序, 推荐"厂商,型号"方式
model                 string                     描述设备模块信息
state                 "okay"/"disabled"          表明当前设备是否可操作, 比如热插拔设备, 插入后变为okay
reg                   addr1 len1 addr2 len2 ...  addr + len是一组, 另外由address-cells和size-cell共同决定
range                 空 / [child parent len]    定义基地址, 这样子节点只写偏移即可
phandle               int                        定义一个号, 其他节点可以通过此号引用节点
interrupt
dma-ranges            空 / [child parent len]
===================== ========================== ========================================================



3 读取属性
-------------

.. code-block:: c

    ################################### 1 先获取节点
    struct device_node *np;
    np = of_find_node_by_path("/zwnode");
    np = of_find_node_by_path("/soc/aips-bus@02100000/ethnernet@02188000");

    ################################### 2 读取节点的属性值
    /* 读取字符串类. */
    char *str;
    ret = of_property_read_string(np, "compatible", &str);
    CI_RET(ret < 0, ret, "read string fail");

    /* 读取u32数组, 这里读取10组. */
    u32 regdata[14];
    of_property_read_u32_array(np, "reg", regdata, 10);
    of_property_read_u8_array();
    of_property_read_u16_array();
    of_property_read_u64_array();

    /* 读取reg属性 */
    uint32_t *addr1 = of_iomap(np, 0);
    uint32_t *addr2 = of_iomap(np, 1);
    uint32_t *addr3 = of_iomap(np, 2);

