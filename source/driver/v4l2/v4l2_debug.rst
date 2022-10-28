v4l2调试
========

1 基本命令
----------

1.1 查看是否加载uvc驱动
***********************

.. code::

   lsmod | grep uvc

2 使用v4l2-utils
----------------

2.1 查看所有的摄像头设备
************************

.. code::

   v4l2-ctl --list-devices

2.2 查看X摄像头支持的格式
*************************

.. code::

   # v4l2-ctl --list-formats-ext --device=0

   ioctl: VIDIOC_ENUM_FMT
     Type: Video Capture

	 [0]: 'YUYV' (YUYV 4:2:2)
	     Size: Stepwise 176x144 - 4000x3000 with step 16/8
     [1]: 'NV12' (Y/CbCr 4:2:0)
	     Size: Stepwise 176x144 - 4000x3000 with step 16/8

2.3 显示X摄像头的详细信息
****************************

.. code::

   v4l2-ctl --all --device /dev/video1

   # 或者
   v4l2-ctl -d /dev/video1 --all

2.4 设置曝光
************

.. code:: c

   # 自动曝光
   v4l2-ctl -d /dev/video1 -c exposure_auto=3

   # 手动曝光
   v4l2-ctl -d /dev/video0 -c exposure_auto=1

   # 设置曝光值
   v4l2-ctl -d /dev/video0 -c exposure_absolute=50

3. media分析
------------

3.1 查看所有的entity
********************

.. code::

   media-ctl --known-mbus-fmts

3.2 查看设备链路
****************

.. code::

   media-ctl -d /dev/media0 -p

4. 使用gsteramer
----------------

4.1 预览
********

.. code:: c

   gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, \
   format=YUY2, width=4000, height=3000 ! imxvideoconvert_g2d ! queue ! \
   video/x-raw, format=RGB16, width=640, height=480 ! waylandsink





