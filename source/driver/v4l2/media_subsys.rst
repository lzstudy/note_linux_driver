media子系统
===========

1 介绍
------

为了解决多媒体各种硬件拓扑问题, 产生了media子系统

2 

2 应用编程
----------

应用层提供4个ioctl, 来控制pipeline, 底层相关文件 ``media-device.c``,  典型代码

.. code:: c

   # 1 打开设备
   open("/dev/media0");

   # 2 获取设备信息, 非必须
   ioctl(fd, MEDIA_IOC_DEVICE_INFO, media_device_info)

   # 3 获取所有entity信息
   ioctl(fd, MEDIA_IOC_ENUM_ENTITIES, media_entity_desc);

   # 4 获取所有link信息
   ret = ioctl(fd, MEDIA_IOC_ENUM_LINKS, media_links_enum);

   # 5 根据3和4重新设置link
   ret = ioctl(fd, MEDIA_IOC_SETUP_LINK, media_link_desc);


