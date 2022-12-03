insmod流程分析
===================

参考文章：
https://blog.csdn.net/lidan113lidan/article/details/45313535

1 说明
---------

2 应用部分
----------

3 内核部分
------------

.. code-block:: c

    ############################################# 1 start
    # 1.1 系统调用入口
    SYSCALL_DEFINE3(init_module, void __user *, umod, unsigned long ...)

    # 1.2 上层触发系统调用(kernel/module.c)
    el0_svc => el0_svc_handler => __arm64_sys_finit_module => __do_sys_finit_module => load_module

    ############################################# 2 load_module
    # 2.1 判断是否为elf文件, 获取加载信息
    elf_header_check && setup_load_info

    # 2.2 申请内存, 且移动到目标位置
    layout_and_allocate

    # 2.3 初始化模块
    module_unload_init && init_param_lock && find_module_sections && check_module_license_and_versions

    # 2.4 解析参数
    parse_args

    # 2.5 挂载到sysfs
    mod_sysfs_setup

    # 2.6 初始化模块
    do_init_module

    ############################################# 3 do_init_module
    # 3.1 初始化模块(非常重要), 其中mod->init就是我们module_init的接口
    do_one_initcall(mod->init); => text_i2c_driver_init

    # 3.2 资源释放
