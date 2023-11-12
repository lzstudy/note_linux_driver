内核移植
=================

1 编译内核
--------------------

.. code-block:: c

    ################################### 修改makefile
    ARCH=arm
    CROSS_COMPILE=arm-linux-gnueabi-

    ################################### 全部编译
    make xxx_defconfig
    make -j16

    ################################### 单独编译内核、设备树
    make zImage
    make dtbs

2 是


