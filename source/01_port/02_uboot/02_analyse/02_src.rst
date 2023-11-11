uboot分析
====================

1 启动流程
------------------

====================================== ==================== =====================================================
文件                                    重要符号             说明
 -- 流程相关文件 --
arch/arm/cpu/armv7/start.S              _start              程序运行起始文件
arch/arm/lib/crt0.S                     _main/board_init_f  设置栈指针, gd数据, 堆区设置, 板级早期初始化, 重定位代码
common/board_f.c                        board_init_f        first初始化, malloc, 串口, 打印cpu信息, 板子信息,
common/board_r.c                        board_init_r        初始化全部硬件, 进入main_loop      
common/main.c                           main_loop           进入超循环
cmd/boot.c                                                  引导内核程序
 -- 重要结构相关文件 --
include/asm-generic/global_data.h      gd_t                         
include/asm-generic/uboot.h            bd_t 
====================================== ==================== =====================================================


2 saveenv原理
------------------

    setenv将环境变量保存到内存中, 而saveenv将内存中的数据写入到SD卡上

======================================= =============================================
文件                                     重要函数
cmd/nvedit.c         
common/env_mmc.c                         saveenv
arch/arm/cpu/armv7/mx6/soc.c             mmc_get_env_dev
board/freescale/common/sdhc_boot.c       mmc_get_env_addr
common/env_mmc.c                         write_env
======================================= =============================================

.. code-block:: c

    do_env_set()
      saveenv()
        mmc_get_env_dev()           // bd:soc.c - 获取mmc设备ID
          mmc_get_env_part()        // bd:soc.c - 获取mmc设备分区
        mmc_get_env_addr()          // bd:boot.c - 获取环境变量在内存中的首地址和大小
        write_env()                 // 将数据写入emmc

3 bootz原理
------------------

    从内存中启动内核, uboot的最后一个命令为 ``bootz zImage - zw.dtb``, 跳转内核需要两个参数, 分别
    是 ``机器ID`` 和 ``设备树地址`` 。

======================================= ============================== =============================================
cmd/bootm.c                             bootm_headers_t images         启动内核相关结构体, 包含内核、dtb地址
--                                      do_bootz                       启动内核开始
include/mapmem.h                        map_sysmem                     相当于malloc
arch/arm/lib/bootm.c                    do_bootm_linux                 内核启动函数
======================================= ============================== =============================================

=============== =============== =======================
images          ft_addr         设备树地址(0x83000000)
--              ft_len          设备树长度
=============== =============== =======================

.. code-block:: shell

    do_bootz()
      bootz_start()                 // 设置image地址 [state - START]
        do_bootm_states()
          bootm_start()             // image->ep = 0x8080 0000 
          bootm_find_os()           // 获取内核地址, 起始地址, 长度
          bootm_find_other()        // 获取设备树和ramdisk
            bootz_setup()           // 判断内核镜像magic是否正确, 从而判断内核是否正确
      do_bootm_states()             // 找到启动函数 do_bootm_linux
        do_bootm_linux()
          boot_prep_linux()
            boot_jump_linux()
              kernel_entry()        // 进入内核


