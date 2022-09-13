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

4 subdev驱动
------------

4.1 说明
********

* subdev提供两种方式调用, 一种是v4l2_subdev_ops, 另外可以提供设备节点到应用层, 直接操作设备节点控制
* v4l2 subdev提供多种操作接口在 `` v4l2-subdev.h`` , 常用core, video, pad, sensor
* 如果倾向使用media子系统, 那么使用pad_ops代替video_ops

.. note::

   * 使用ioctl控制subdev时，有时会返回-1, 是因为在系统架构层有提前的条件判断, 详细查看 ``v4l2-subdev.c`` 中的 ``subdev_do_ioctl`` 来查看. 关键地方结合ftrae和内核探针做判断

4.2 参考逻辑
************

.. code:: c

   # 1 初始化subdev
   v4l2_subdev_init();

   # 2 设置文件标志(使能此项才会生成节点)
   sd->flags |= V4L2_SUBDEV_FL_HAS_DEVNODE

   # 3 注册pads
   sd->entity.type = MEDIA_ENT_T_V4L2_SUBDEV_LENS;
   media_entity_init(&sd->entity, 0, NULL, 0);

   # 4 注册subdev
   v4l2_device_register_subdev(v4l2_dev, &sd);

   # 5 生成节点文件
   v4l2_device_register_subdev_nodes(v4l2_dev);

.. tip::

   * 存取数据 ``v4l2_set_subdevdata`` , ``v4l2_get_subdevdata``
   * 存取数据 ``v4l2_get_subdev_hostdata``, ``v4l2_set_subdev_hostdata``
   * VIDIOC_QUERYCTRL, VIDIOC_QUERYMENU, VIDIOC_G_CTRL, VIDIOC_S_CTRL, VIDIOC_G_EXT_CTRLS, VIDIOC_S_EXT_CTRLS和 VIDIOC_TRY_EXT_CTRLS与主控操作相同, 不过会优先在subdev中处理

4.3 技巧
********

* 自定义ioctl来控制subdev

.. code:: c

   # 自定义ioctl在core ops中

   #define VIDIOC_SUBDEV_TEST		_IOWR('V', 200, subdev_test)

   statuc long sd_usr_ioctl(struct v4l2_subdev *sd, unsigned int cmd, void *arg)
   {
       if(cmd == VIDIOC_SUBDEV_TEST)
   }

   static const struct v4l2_subdev_core_ops core_ops = {
       .xxx = xxx.
       .ioclt = sd_usr_ioctl,
   };

* 使用宏来调用subdev接口

.. code:: c

   # 调用subdev
   err = v4l2_subdev_call(sd, core, g_std, norm);

   # 通知事件
   v4l2_subdev_notify(sd, notificaton, arg);


5 使用media子系统
-----------------

5.1 注册顺序
*************

.. code:: c

   # 1 注册V4L2设备
   v4l2_device_register(NULL, v4l2_dev);

   # 2 注册media设备
   strcpy(md. model, "name"
   set v4l2_dev
   set dev
   media_device_register(&priv->media_dev.md);

   # 3 注册video
   video_register_device

   # 4 注册其他subdev
   v4l2_device_register_subdev



