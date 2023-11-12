uboot移植
================

1 移植相关文件
-------------------
====================================== ======================================
board/                                  板级目录, 平台相关初始化软件
include/configs/xxx.h                   裁剪核心配置文件
arcm/arm/cpu/armv7/${platform}/         时钟配置 + Kconfig将新baord加入源码树
configs/xxx_defconfig                   默认配置
====================================== ======================================

2 uboot移植
-------------------

2.1 测试参考板源码
***********************

.. code-block:: c

    # 打开Makefile, 添加ARCH和CROSS_COMPILE
    ARCH=arm
    CROSS_COMPILE=arm-poky-linux-gnueabi-

    # 打开源码直接编译
    make xxx_defconfig
    make -j16

2.2 添加自己的板子
***********************

    添加自己的板子后可以通过 ``grep -nr board/${platform}/${evk}`` 来专门查找和路径相关的替换, 
    也可以通过 ``grep -nr ${evk}`` 来观察全部的替换

.. code-block:: c

    ################################### 1 拷贝参考板作为自己的板子
    cp board/${platform}/{evk} zwevk -rf

    ################################### 2 调整目录下的文件
    #---------------------------------- 2.1 重命名
    mv ${evk.c} zwevk.c

    #---------------------------------- 2.2 调整Makefile
    obj-y  := zwevk.o

    #---------------------------------- 2.3 调整Kconfig
    if TARGET_ZWEVK
    config SYS_BOARD
        default "zwevk"

    config SYS_CONFIG_NAME
        default "zwevk"

    #---------------------------------- 2.4 调整Maintained
    F:  board/freescale/zwevk/
    F:  include/configs/zwevk.h
    F:  configs/zwevk_defconfig

2.3 添加自己板子的搜索路径
****************************

.. code-block:: c

    vi arch/arm/cpu/armv7/${platform}/Kconfig
    -------------------------------------------

    # 1 添加自己的支持, 其余和参考板一样
    CONFIG TARGET_ZWEVK
        bool "support zwevk"
        
    # 2 添加搜索路径
    source "board/xxx/zwevk/Kconfig"

2.4 添加默认配置文件
***********************

.. code-block:: c

    # 拷贝参考默认配置
    cp configs/xxx_defconfig configs/zwevk_defconfig

    # 修改默认配置, 替换为board/${platform}/zwevk的路径
    vi configs/zwevk_defconfig
    ---------------------------------

    CONFIG_SYS_EXTRA_OPTIONS="board/${platform}/zwevk/aaa"
    CONFIG_TARGET_ZWEVK=y

2.5 添加裁剪配置头文件
***********************

.. code-block:: c

    # 拷贝参考头文件
    cp include/configs/xxx.h zwevk.h

    # 调整头文件
    vi include/configs/zwevk.h
    ---------------------------------

    #ifndef __ZWEVK_H__
    #define __ZWEVK_H__

2.6 添加其他配置
***********************

    其他配置根据情况自己添加, 比如lcd驱动, 上网功能等


2.7 编译测试
***********************

.. code-block:: c

    # 1 修改Makefile 写死ARCH等方便调试
    ARCH = arm
    CROSS_COMPILE = arm-poky-linux-gnueabi-

    # 2 编译
    make zwevk_defconfig
    make -j16

    # 3 测试, 可以通过查看zwevk.h是否被大量引用来判断
    grep -nR "zwevk.h"

    # 4 下载测试, 观察开机打印的编译时间, 和板子名称


3 配置加载系统
-----------------

3.1 通过设备树配置
**************************

::

    修改


3.2 配置NFS配置
**************************

    uboot中默认nfs版本是V2的, 而现在主流ubuntu的nfs版本是V3或V4, 因此需要在uboot中指定nfs版本为V3。
    或者设置ubuntu版本支持V2, ``rpcinfo -p | grep nfs`` 可以通过此命令查看ubuntu nfs版本

.. code-block:: c

    # 加载内核和设备树
    tftp 80800000 zImage
    tftp 83000000 zw.dtb

    # 加载根文件系统 - 方案1 变量形态, 会更简介
    # boardip=192.168.31.66 serverip=192.168.31.88
    setenv console ttymxc0
    setenv baudrate 115200
    setenv serverip 192.168.31.88
    setenv nfsroot 192.168.31.88:/home/zw/linux/nfs/rootfs
    setenv nfsip 192.168.31.66:192.168.31.88:192.31.1.1:255.255.255.0::eth0 off
    setenv bootargs console=${console},${baudrate} root=/dev/nfs ip=${nfsip} nfsroot=${serverip}:${nfsroot},v3,tcp

    # 加载根文件系统 - 方案2 nfs 可以设置IP为dhcp的, 不过不灵活, 进入系统后无法更换IP
    setenv bootargs console=${console},${baudrate} root=/dev/nfs ip=dhcp nfsroot=${serverip}:${nfsroot},v3,tcp

    # 加载根文件系统 - 方案3 全写形态
    setenv bootargs console=ttymxc0,115200 root=/dev/nfs rw nfsroot=192.168.31.88:/home/zw/linux/nfs/rootfs,v3,tcp ip=192.168.31.66:192.168.31.88:192.31.1.1:255.255.255.0::eth0 off

    # 启动系统
    boot zImage - zw.dtb

4 常见问题
--------------------

4.1 编译提示configs/.h
*************************

.. code-block:: c

    # 错误信息
    include/config.h:6:22: fatal error: configs/.h: No such file or directory

    # 原因 以下两个文件没有支持 TARGET_ZWEVK
    arm/arm/cpu/${platform}/Kconfig
    board/${platform}/${evk}/Kconfig



