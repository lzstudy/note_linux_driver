方案2 - buildroot
======================

.. image:: _images/buildroot.png

1 基础理论
-----------------------

1.1 buildroot目录说明
***********************

========================= ======================================================
docs                      教学文档
dl                        存放下载的源码, 可以自己下载软件包放到这里来加快编译速度
output/build              存放解压后各种软件包编译完成后的现场
output/host               存放编译工具链
output/images             存放编译好的rootfs
output/target             存放linux系统基本目录结构, 想添加自己的内容, 就修改此处
========================= ======================================================

2 buildroot移植
-----------------------

.. code-block:: c

    # 导出环境变量
    source /opt/xxx

    # 下载源码 buildroot-2019.02.6
    https://buildroot.org/downloads/

    # 配置源码(第一次先按下图编译最小系统)
    make menuconfig

    # 编译源码
    sudo make

    # 镜像输出位置
    buildroot/output/images/


.. image:: _images/config.png

.. image:: _images/cfg.png


2 常见问题
-----------------

2.1 如何快速下载
********************

    在一台机器下载后, 将 ``buildroot/dl`` 目录拷贝到新的的源码中, 这样可以减少
    下载时间, 来加速编译

2.1 下载时卡住
******************

2.2 新增外部文件

2.3 单独编译某个package
*****************************

.. code-block:: c

    # 在buidlroot根目录下(xxx不用带版本号)
    make xxx-rebuild
    make minicom-rebuild

