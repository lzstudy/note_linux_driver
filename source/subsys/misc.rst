子系统 - MISC
=============

模板文件
--------

======================= ============================================================================
sample_misc.tar.gz_     使用MISC子系统经典模板文件
sample_misc_std.tar.gz_ 使用MISC子系统, 附带私有数据模板, 推荐使用
======================= ============================================================================

.. _sample_misc.tar.gz: http://120.48.82.24:9100/note_linux_driver/sample_misc.tar.gz
.. _sample_misc_std.tar.gz: http://120.48.82.24:9100/note_linux_driver/sample_misc_std.tar.gz

模板使用说明
------------

- 基于平台子系统
- 替换sample_misc关键字
- 直接编写ioctl接口即可


.. note::

   - 不可以将私有数据存储到miscdevices, 该数据结构中的this_device存储了miscdevices
   - open接口中filp->private已经存储了miscdevices, 无需从inode中获取, 且无法从inode获取
   - 想要使用私有数据, 只能通过结构嵌套miscdevices, 然后通过container_of获取

