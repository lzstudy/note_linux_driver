v4l2驱动
========

1 介绍
------

V4L2驱动分为四个部分：v4l2驱动、video驱动、subdev驱动、media子系统。

* v4l2驱动是整体的框架, 包含video驱动 + subdev驱动
* viode驱动实现/dev/videox节点
* subdev驱动实现/dev/subdevx节点
* media子系统将subdev组合成pipeline使上层使用更容易

1.1 重要头文件
**************

.. code:: c

   #include <linux/mutex.h>
   #include <linux/slab.h>
   #include <linux/videodev2.h>
   #include <media/videobuf2-v4l2.h>
   #include <media/videobuf2-vmalloc.h>
   #include <media/v4l2-common.h>
   #include <media/v4l2-device.h>
   #include <media/v4l2-ioctl.h>
   #include <media/v4l2-ctrls.h>
   #include <media/v4l2-event.h>

2 v4l2驱动
----------

.. code:: c

   # 注册接口
   v4l2_device_register(NULL, v4l2_dev);

   # 注销接口
   video_unregister_device(v4l2_dev);

.. note::

   注册接口第一个参数如果设置为NULL, 需要手动指定名字, 一般通过平台总线注册都不会为空, 如果做测试就需要使用接口v4l2_device_set_name来设置一个名字

3 video驱动
***********

.. code:: c
    
   # 互斥锁初始化
   mutex_init(&video_dev->lock);

   # 初始化video设备
   snprintf(video_dev->vdev.name, sizeof(video_dev->vdev.name), "zw_video%d", 1);
   video_dev->vdev.fops = &vin_fops;
   video_dev->vdev.ioctl_ops = &vin_ioctl_ops;
   video_dev->vdev.release = video_device_release_empty;
   video_dev->vdev.ctrl_handler = NULL;
   video_dev->vdev.queue = &video_dev->vq;
   video_dev->vdev.lock = &video_dev->lock;
   video_dev->vdev.flags = V4L2_FL_USES_V4L2_FH;

   # 注册video设备
   ret = video_register_device(&video_dev->vdev, VFL_TYPE_GRABBER, -1);
   CHECK_R(ret < 0, ret, "video register device fail reason = %d", ret);


.. note::

   * 互斥锁一定要自己初始化
   * vin_ioctl_ops可以先实现一个vidioc_querycap来调试
