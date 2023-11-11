方案2 - buildroot
======================

.. image:: _images/buildroot.png

1 buildroot移植
-----------------------

.. code-block:: c

    # 下载源码 buildroot-2019.02.6
    https://buildroot.org/downloads/

    # 配置源码(第一次先按下图编译最小系统)
    make menuconfig

    # 编译源码
    sudo make

    # 镜像输出位置
    buildroot/output/images/


.. image:: _images/config.png


2 常见问题
-----------------

2.1 下载时卡住
******************
