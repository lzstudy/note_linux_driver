支持USB存储设备
===============

需要加载的KO
------------

::

   # 支持U盘驱动
   insmod scsi_mod.ko
   insmod usb-storage.ko
   insmod sd_mode.ko

   # 支持FAT32文件系统
   insmod fat.ko
   insmod vfat.ko

   # 支持语言相关
   insmod nls_cp437.ko
   insmod nls_iso8859-1.ko


如何编译上述KO
--------------

- 编译nls_cp437.ko

::

   File systems --->
      Native language support --->
	     <M> Codepage 437 (United States, Canada)

- 编译nls_iso8859-1.ko

::

   File systems ---> 
       Native language support ---> 
	       <M> NLS ISO 8859-1



