uboot移植
================

overview
----------------------

.. image:: .images/uboot.png


- 环境变量是放在外部flash中, 启动时读取到DRAM
- 设置环境变量如果带有空格, 需要单引号引起来 ``setenv var 'aa bb'``
- uboot扇区和块是一个意思, 代表512字节
- uboot版本当前版本号 年代(2016) + 补丁版本号(03) + 子版本号, 2016.03


1 uboot使用
----------------

.. code-block:: c

    # 1 开启uboot debug信息打印
    vi include/common.h
    -------------------------
    #define DEBUG




1.1 查看版本
**********************

.. code-block:: c

    # 通过查看版本(编译时间), 来对比uboot是否更新成功
    version
    -----------------

    U-Boot 2016.03 (Oct 28 2023 - 20:02:40 -0700)
    arm-poky-linux-gnueabi-gcc (GCC) 5.3.0
    GNU ld (GNU Binutils) 2.26.0.20160226


1.2 环境变量增删改
**********************

.. code-block:: c

    # 增加/修改
    setenv var val
    saveenv

    # 删除
    setenv var
    saveenv

1.3 设置网络
**********************

.. code-block:: c

    setenv ipaddr 192.168.1.66
    setenv ethaddr b8:ae:1d:01:00:00
    setenv gatewayip 192.168.1.1
    setenv netmask 255.255.255.0
    setenv serverip 192.168.1.88
    saveenv

.. note:: 
    
    tftp只能与ubuntu通信, wsl不支持被 ``ssh和tftp``


1.4 mmc操作
**********************

.. code-block:: c

    # 查看mmc信息
    mmc info

    # 切换mmc(emmc和sd卡切换)
    mmc dev 0/1/2

    # 查看mmc分区(只有格式化分区才能看到, 一般uboot.bin是看不到的0分区)
    mmc part

    # 切换分区(切换1设备, 第二个分区)
    mmc dev 1 2

    # 读取数据(从偏移0x600扇区, 读取10个扇区)
    mmc read 80800000 600 10

    # 写数据(mmc write addr start_sector cnt)
    mmc write 80800000 2 2E6

    # 擦除数据(mmc erase sector cnt)
    mmc erase 

1.5 FAT文件系统操作
*********************

.. code-block:: c

    # 查看文件系统类型
    fatinfo interface dev:part
    fatinfo mmc 1:1

    # 查看分区的文件系统类型, 一般1:0会显示无类型, 因为没分区
    fstype mmc 1:0
    fstype mmc 1:1
    fstype mmc 1:2

    # 查看目录和文件
    fatls mmc 1:1

    # 读取文件到指定地址
    fatload mmc 1:1 80800000 zImage

1.6 调试下载uboot
**********************

.. code-block:: c

    # 下载uboot
    tftp 80800000 u-boot.imx

    # 将uboot保持到mmc
    mmc dev 0 0
    mmc write 80800000 2 2E6

    # 重启开发板, 查看version来判断是否更新成功
    version

1.7 调试下载内核
*****************

.. code-block:: c

    # 通过tftp下载内核, (也可以通过nfs)
    tftp 80800000 zImage
    tftp 83000000 dev.dtb
    bootz 80800000 - 8300000

.. tip:: 
    
    可以通过对比前100个字节来确定是否下载成功 :

    - 上位机 ``od -h -N 100 zImage``
    - 开发板 ``md.w 80800000 100``

1.8 调试下载设备树
*********************

.. code-block:: c

    # 下载设备树
    tftp 83000000 my.dtb

    # 设置设备树地址
    fdt addr 83000000

    # 查看设备树头部信息
    fdt header

    # 查看设备树内容(fdt print path)
    fdt print
    fdt print /spi

1.9 标准加载内核和设备树
*************************

.. code-block:: shell

    # uboot启动时通过boot命令, 自动加载bootcmd
    setenv bootcmd 'tftp 80800000 zImage; tftp 8300000 my.dtb; bootz 8080000 - 83000000'
    setenv bootcmd 'fatload mmc 1:1 80800000 zImage; fatload mmc 1:1 my.dtb; bootz 8080000 - 83000000'
    saveenv

1.10 内存测试
*************************

.. code-block:: c

    mtest 80000000 80001000


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


3 重要文件说明
-------------------

3.1 移植相关文件
******************
====================================== ======================================
board/                                  板级目录, 平台相关初始化软件
include/configs/xxx.h                   裁剪核心配置文件
arcm/arm/cpu/armv7/${platform}/         时钟配置 + Kconfig将新baord加入源码树
configs/xxx_defconfig                   默认配置
====================================== ======================================

3.2 运行流程文件
*******************
====================================== ==================== =====================================================
文件                                    重要符号             说明
arch/arm/cpu/armv7/start.S              _start              程序运行起始文件
arch/arm/lib/crt0.S                     _main/board_init_f  设置栈指针, gd数据, 堆区设置, 板级早期初始化, 重定位代码
common/board_f.c                        board_init_f        first初始化, malloc, 串口, 打印cpu信息, 板子信息,
common/board_r.c                        board_init_r        初始化全部硬件, 进入main_loop      
common/main.c                           main_loop           进入超循环
cmd/boot.c                                                  引导内核程序
====================================== ==================== =====================================================

3.3 重要结构文件
*******************
=================================== ============================ =========================
文件                                 重要结构                     说明
include/asm-generic/global_data.h   gd_t                         
include/asm-generic/uboot.h         bd_t 
=================================== ============================ =========================

3.4 重要函数
****************
======================== ========================================
map_sysmem               uboot初始化阶段申请内存函数, 相当于malloc
======================== ========================================

3.4 内存区域
*******************
=============== ========================
gd数据           需要重定位

=============== ========================


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



