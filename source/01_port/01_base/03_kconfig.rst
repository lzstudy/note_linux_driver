Kconfig 
==============

    menuconfig通过Kconfig生成, 最后生成的autoconf文件, Makefile通过此文件来
    判断文件是否要编译

.. note:: 
    
    Kconfig中的选项, 在autoconf文件前会加上前缀CONFIG, 比如config BOOL_ITEM, 
    最终会变成 ``#define CONFIG_BOOL_ITEM``


1 添加目录
---------------

::

    menu "zw menu"
    endmenu

.. image:: .images/menu.png

2 添加子项
---------------

    Kconfig一共支持5种类型: bool、tristate、字符串、整数、十六进制

:: 

    config BOOL_ITEM                        # 类型 - bool
        bool "bool item"
        help
            this is bool item

    config TRISTATE_ITEM                    # 类型 - tristate
        tristate "tristate item"
        help
            this is tristate item

    config STRING_ITEM                      # 类型 - string
        string "string item"
        help
            this is string item

    config HEX_ITEM                         # 类型 - int
        hex "hex item"
        range 0 0x2000
        default 0x1000
        help
            this is hex item

    config INT_ITEM                         # 类型 - hex
        int "int item"
        range 0 2000
        default 21
        help
            this is int item

.. image:: .images/item.png

3 添加choice选项
------------------

::

    choice 

    prompt "choice sample"
    default CHOICE_ITEM2                    # 默认选择2项

    config CHOICE_ITEM1
        bool "choice item1"
        help
            choice item1

    config CHOICE_ITEM2
        bool "choice item2"
        help 
            choice item2

    config CHOICE_ITEM3
        tristate "choice item3"
        help 
            choice item3

    endchoice

.. image:: .images/choice.png

4 选项依赖
--------------

::

    config DEPEND_ITEM                     
        bool "depend item"                
        depends on TRISTATE_ITEM            # 依赖tristate项, 依赖项没选中时, 不会出现此项
        help
            this is depend item

.. image:: .images/depend.png
