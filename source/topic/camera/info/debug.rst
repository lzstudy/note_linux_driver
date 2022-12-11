驱动调试
==========

根据平台特性, 选择vl42-ctl或者gstreamer调试

1 v4l2-ctl调试
----------------

v4l2-ctl调试可以调试UVC之类的摄像头, 如果是media子系统的摄像头有可能查看信息有问题, 可以先使用1.3方式来判断是否能使用v4l2-ctl调试

1.1 列出所有的摄像头设备
************************

.. code-block:: c

    $ v4l2-ctl --list-devices

    ():
        /dev/v4l-subdev0
        /dev/v4l-subdev1
        /dev/v4l-subdev2

    (csi0):
        /dev/v4l-subdev3

    VIV (platform:viv0):
        /dev/video0

1.2 查询摄像头支持的格式
**************************

.. code-block:: shell

    $ v4l2-ctl --list-formats-ext --device /dev/video0

    ioctl: VIDIOC_ENUM_FMT
        Type: Video Capture

        [0]: 'YUYV' (YUYV 4:2:2)
                Size: Stepwise 176x144 - 4000x3000 with step 16/8
        [1]: 'NV12' (Y/CbCr 4:2:0)
                Size: Stepwise 176x144 - 4000x3000 with step 16/8
        [2]: 'NV16' (Y/CbCr 4:2:2)
                Size: Stepwise 176x144 - 4000x3000 with step 16/8
        [3]: 'BG10' (10-bit Bayer BGBG/GRGR)
                Size: Stepwise 176x144 - 4000x3000 with step 16/8

1.3 查看设备详细信息
**********************

.. code-block:: shell

    $ v4l2-ctl --all --device /dev/video0

    Driver Info:
            Driver name      : viv_v4l2_device
            Card type        : VIV
            Bus info         : platform:viv0
            Driver version   : 5.4.70
            Capabilities     : 0x84200001
                    Video Capture
                    Streaming
                    Extended Pix Format
                    Device Capabilities
            Device Caps      : 0x04200001
                    Video Capture
                    Streaming
                    Extended Pix Format
    Media Driver Info:
            Driver name      : vvcam-video
            Model            : viv_media

1.4 拍照
**********

.. code-block:: c

    v4l2-ctl --device /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=MJPG --stream-mmap --stream-to=./output_1920.jpg --stream-count=1


1.5 录像
***********

.. code-block:: c

    # mjpg格式
    v4l2-ctl --device /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=MJPG --stream-mmap --stream-to=./output.mjpg --stream-count=300

    # h264格式
    v4l2-ctl --device /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=H264 --stream-mmap --stream-to=./output --stream-count=100


1.6 列出ctrl插件
*******************

.. code-block:: c

    v4l2-ctl --list-ctrls --device /dev/video


2 gstreamer调试
----------------

2.1 预览
**********

.. code-block:: c

    # 1 采用默认分辨率
    gst-launch-1.0 v4l2src device=/dev/videoX ! autovideosink

    # 2 采用指定分辨率
     gst-launch-1.0 v4l2src ! video/x-raw, width=<WIDTH>, height=<HEIGHT>, framerate=<FRAMERATE> ! autovideosink
