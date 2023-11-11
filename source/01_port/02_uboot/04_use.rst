uboot使用
================


1 运行使用
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


2 从源码中修改环境变量
-----------------------------

    uboot的环境变量默认在代码中, setenv会将环境变量保存到emmc中, 且优先级高于默认。
    默认的环境变量保存在 ``include/env_default.h`` 用变量 ``default_environment`` 保存。


3 uboot中更新uboot
-----------------------------

.. code-block:: shell

    # 通过tftp将u-boot.imx下载到内存
    tftp 80800000 u-boot.imx

    # 切换mmc为sd卡第0个分区
    mmc dev 0 0

    # 将u-boot.imx写入到SD卡, 跳过前两个扇区, 大小为0x320个块
    mmc write 80800000 2 320

    # 设置环境变量自动更新(方法1)
    setenv zwd 'tftp 80800000 u-boot.imx; mmc dev 0 0; mmc write 80800000 2 320'
    saveenv
    run zwd

    # 设置环境变量自动更新(方法2)
    setenv bootcmd 'tftp 80800000 u-boot.imx; mmc dev 0 0; mmc write 80800000 2 320'
    saveenv
    boot

.. note:: 
    
    假设u-boot.imx大小为400K(409600字节), 转换为块则是800个块, 十六进制为(320) 

4 uboot自动更新内核原理
---------------------------

.. code-block:: shell

    # 设置bootcmd命令
    setenv bootcmd 'fatload mmc 0:1 80800000 zImage; fatload mmc 1:1 83000000 zw.dtb; bootz 80800000 - 83000000'

    # uboot启动时会自动调用boot来调用bootcmd命令(所以此命令不需要调用)
    boot

5 内核启动参数
-----------------------

    - 在没有设备树的版本, 通过bootargs传递
    - 在有设备树的版本, bootargs通过设备树传递, uboot的中bootargs就没用了

.. code-block:: c

    bootargs=console=ttymxc0,115200 root=/dev/nfs ip=dhcp nfsroot=:,v3,tcp
