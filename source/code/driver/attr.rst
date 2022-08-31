设备属性
========

2 添加一个属性文件
------------------

3 添加一组属性文件
------------------

.. code:: c

   static ssize_t xxx_dbg_attr_store(struct device *dev, struct device_attribute *attr, const char *buf, size_t count)
   {
       int cmd;

       if(kstrtoint(buf, 0, &cmd)
           return -EINVAL;
       
       switch(cmd)
       {
       case 0:
           break;

       default:
           return -ENIVAL;
       }

       return count;
   }

   static ssize_t xxx_dbg_attr_show(struct device *dev, struct device_attribute *attr, char *buf)
   {
       return sprintf(buf, "%d\n", mtdev->cxt.mrp.step);
   }

   static DEVICE_ATTR(dbg0, 0220, NULL, xxx_dbg0_attr_store);
   static DEVICE_ATTR(dbg1, 0440, xxx_dbg1_attr_show, NULL);
   static DEVICE_ATTR(dbg2, 0640, xxx_dbg2_attr_show, xxx_dbg2_attr_store);

   static struct attribute *xxx_attrs[] = {
       &dev_attr_dbg.attr,
       NULL
   };

   static struct attribute_group xxx_attr_group = {
       .name = "xxx_attr" /* 文件夹名称, 会生成/sys/kernel/xxx_attr路径 */
       .attrs = xxx_attrs,
   };

   static int __init xxx_init(void)
   {
       sysfs_create_group(kernel_kobj, &xxx_attr_group);
   }

   static int __init xxx_exit(void)
   {
       sysfs_remove_group(kernel_kobj);
   }

bool变量 = !!of_get_property


内核定义的kobj
--------------

=========== =========== ================
kobj名称    路径        源码路径
kernel_kobj /sys/kernel kernel/ksysfs.c
=========== =========== ================
