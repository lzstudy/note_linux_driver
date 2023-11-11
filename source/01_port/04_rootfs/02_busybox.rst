移植工具 - busybox
====================

1 工具基础
--------------

1.1 编译环境
******************

    busybox目前在ubuntu18环境编译会报错, 建议在ubuntu16环境下编译。

1.2 下载busybox
******************

================ =================================
国外地址          https://busybox.net/downloads/
================ =================================

2 制作跟文件系统
------------------

    busybox编译完输出的目录只有3个, 其他目录需要手动补充

2.1 编译busynbox
********************

.. code-block:: c

    # 进入busybox所在目录
    cd busybox/

    # 修改Makefile 
    ARCH=arm
    CROSS_COMPILE=arm-linux-gnueabi-

    # 编译
    make menuconfig 
    make -j16

    # 安装
    mkdir rootfs
    make install CONFIG_PREFIX=./rootfs

2.2 添加lib
*******************

.. code-block:: c

    mkdir lib

    # 拷贝编译器sysroot下的lib内容到此
    cd /opt/xxx/arm-linux-gnueabi/libc/lib
    cp *so* *.a lib -d

2.3 添加usr/lib
*****************************

.. code-block:: c

    # 拷贝编译器sysroot下usr/lib的内容
    cp *so* *.a usr/lib -d

2.4 创建其他目录
************************

.. code-block:: c

    mkdir dev proc mnt sys tmp root