kprobe
========

1 介绍
------

kprobe又称内核探针, 可以在函数入口或者出口插入

* kprobe    - 可以在任何地址/符号插入, 可以打印栈回溯
* jprobe    - 只能在函数处插入, 用于检测函数参数
* kretprobe - 查看返回值

.. note::

   kprobe + ftrace功能请到页面ftrace查看, 可以实现不编写代码调试

2 kprobe模块
------------

2.1 使用流程
************

.. code:: c

   #include <linux/kprobes.h>

   static struct kprobe kp = {
       .symbol_name = "do_fork",
   };

   static int kprobe_handler_pre(struct kprobe *p, struct pt_regs *regs)
   {
      dump_stack();
      return 0;
   }

   static void kprobe_handler_post(struct kprobe *p, struct pt_regs *regs, unsigned long flags)
   {
      LOG_FUNC();
   }

   static int kprobe_handler_fault(struct kprobe *p, struct pt_regs *regs, int trapnr)
   {
      LOG_FUNC();
      return 0;
   }

   static int __init kprobe_init(void)
   {
      kp.pre_handler = kprobe_handler_pre;
      kp.post_handler = kprobe_handler_post;
      kp.fault_handler = kprobe_handler_fault;
      int ret = register_kprobe(&kp);
      return 0;
   }

   static void __exit kprobe_exit(void)
   {
      unregister_kprobe(&kp);
   }

3 jprobe模块
------------

3.1 参考代码
************

.. code:: c

   static long j_do_fork(unsigned long clone_flags, unsigned long stack_start, 
              unsigned long stack_size, int __user *parent_tidptr, int __user *child_tidptr)
   {
      pr_info("jprobe: clone_flags = 0x%lx, stack_start = 0x%lx ", clone_flags, stack_start, stack_size);
      jprobe_return();
      return 0;
   }

   static struct jprobe my_jprobe = {
      .entry = j_do_fork,
      .kp = {
         .symbol_name = "_do_fork",
      },
   };

   static int __init jprobe_init(void)
   {
      return register_jprobe(&my_jprobe);
   }

   static void __exit jprobe_exit(void)
   {
      unregister_jprobe(&my_jprobe);
   }


.. note::

  entry的原型是void的指针, entry的回调接口和你要探测的接口一样




4 kretprobe
-----------

参考源码

5 源码参考
----------

内核中提供了kprobe, jprobe, kretprobe的参考代码, 直接修改驱动中的符号信息就可以直接使用

========= =====================================
kprobe    ./samples/kprobes/kprobe_example.c
jprobe    ./samples/kprobes/jprobe_example.c
kretprobe ./samples/kprobes/kretprobe_example.c
========= =====================================
