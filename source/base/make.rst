编译模块
============

Kconfig作用于全局

设置多文件编译
--------------

.. code-block:: makefile

   obj-m += xxx.o                           # 设置要编译的模块
   xxx-objs += aaa.o bbb.o                  # 设置该模块依赖的文件

设置编译选项
------------

.. code-block:: makefile

   ccflags-y += -std=c11                    # 设置c11标准
   ccflags-y += -I$(PWD)/../common          # 设置编译的头文件
   ccflags-y += -O0                         # 设置不优化编译模块, 默认会优化为O2



makefile中的关键字
------------------

========= ==========================
obj-m     要编译的源码
ccflags-y gcc编译选项都加入这里
========= ==========================