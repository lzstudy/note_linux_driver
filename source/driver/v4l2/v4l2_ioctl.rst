v4l2_ioctl说明
==============


1 v4l2-ioctl说明
----------------

2 ioctl介绍
-----------

通用调用栈

.. code:: c

   ioctl(app) ----------------------------------- app
       video_ioctl2 ----------------------------- v4l2-ioctl.c
           __video_do_ioctl --------------------- v4l2-ioctl.c

2.1 VIDIOC_ENUMINPUT
********************

对于有pipeline的系统, 通常需要设置使用哪个摄像头, 所以需要先枚举出有哪些摄像头可用

.. code:: bash

   v4l_enuminput -------------------------------- v4l2-ioctl.c
       vidioc_enum_input ------------------------ vin_video.c ##


2.2 VIDIOC_S_INPUT
******************

设置当前pipeline使用哪个摄像头

.. code:: bash

   v4l_s_input ---------------------------------- v4l2-ioctl.c
       vidioc_s_input --------------------------- vin_video.c ##
           v4l2_ctrl_handler_setup -------------- v4l2-ctrls.c

.. note::

   目前支持以下输入类型, 目前常用V4L2_INPUT_TYPE_CAMERA
   * V4L2_INPUT_TYPE_TUNER  1
   * V4L2_INPUT_TYPE_CAMERA 2
   * V4L2_INPUT_TYPE_TOUCH  3

2.3 VIDIOC_QUERYCAP
*******************

查看设备支持的功能

.. code:: bash

   v4l_querycap --------------------------------- v4l2-ioctl.c
       vidioc_querycap -------------------------- vin_video.c ##

2.4 VIDIOC_REQBUFS
******************

申请BUF

.. code:: bash

   v4l_reqbufs ---------------------------------- v4l2-ioctl.c
       vb2_ioctl_reqbufs ------------------------ videobuf2-v4l2.c
           vb2_core_reqbufs --------------------- videobuf2-core.c
               queue_setup ---------------------- vin_video.c ##
               __vb2_queue_alloc ---------------- videobuf2-core.c
                  __vb2_buf_mem_alloc ----------- videobuf2-core.c

.. note::

   * VB2_MAX_FRAME   32
   * VB2_MAX_PLANES  8


