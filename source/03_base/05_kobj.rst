kobject
==================

1 基本说明
----------------

  应用层的文件是以inode保存, 而内核文件系统是以kernfs_node保存, 所有的文件本质上都是kernfs_node。
  kobject, 使用kset作为目录进行组织, ktype来进行属性的读写。

2 重要接口
-----------------

2.1 文件创建相关接口
***************************

================================================ ============================================================================
### <linux/kernfs-internal.h>                     
kernfs_create_dir_ns();                           带命名空间 - 创建文件夹
kernfs_create_file_ns();                          带命名空间 - 创建文件
### <linux/sysfs.h>                               
sysfs_create_dir_ns();                            创建文件夹
sysfs_create_file_ns();                           创建文件
sysfs_create_group();                             创建属性(文件 + 文件夹)
### <linux/kobject.h>                             
kobject_add_internal();                           创建文件夹 + 文件
kobject_create_and_add();                         调用 kobject_add_internal
kset_create_and_add();                            调用 kobject_add_internal
### <linux/device.h>                              
device_register();                                调用 kobject_add_internal + 等其他属性文件和软练级
================================================ ============================================================================

2.2 kernfs接口
**********************

=========================== ==========================
kernfs_create_root()        创建内核文件系统, 比如sysfs
kernfs_create_dir_ns()      创建文件夹
kernfs_create_link()        创建软连接
=========================== ==========================


1 流程分析
----------------

1.1 kobj创建流程(文件夹创建流程)
*******************************************

.. code-block:: shell

    kobject_create_and_add(name, parent);		# 创建目录, 在/sys目录下, parent=null表示在/sys下
    kobj=kobject_create()					    # 创建kobj
    kobject_add(kobj,parent,name)			    # 添加kobj
        kobject_add_internal(kobj);				# 添加kobj, 此时初始化了name,parent
        kobject_get(kobj->parent);			    # 让parent计数+1
        kobj_kset_join(kobj);					# 初始化kset
        create_dir(kobj);						# 以kobj为参数创建文件夹
            sysfs_create_dir_ns(kobj,ns)
                kernfs_create_dir_ns(kobj,ns)	# 内核创建文件 ##########
            populate_dir(kobj);
        kobj->state_in_sysfs = 1;				# 表示kobj已经初始化过了

1.2 attr创建流程(文件创建流程)
*******************************

.. code-block:: shell

    sysfs_create_file(kobj,attr);                   # 在kobj所在目录下, 创建attr文件
      sysfs_create_file_ns(kobj,attr,ns);
        sysfs_add_file_mode_ns(parent,attr,mode);   # 创建文件
          __kernfs_create_file();                   # 内核通用创建文件接口
            kernfs_new_node();
            kernfs_add_one();
