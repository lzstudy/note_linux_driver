ftrace基本用法
==============

1 介绍
------

ftrace是内核trace子系统的一种, ftrace下又细分为function tracre、graph tracer、event tracer等. ftrace工作在虚拟文件系统之上(debugfs/tracefs)

2 内核配置
----------

.. code:: c

   # 体系结构相关的实现
   CONFIG_HAVE_FUNCTION_TRACER=y
   CONFIG_HAVE_FUNCTION_GRAPH_TRACER=y
   CONFIG_HAVE_DYNAMIC_FTRACE=y
   CONFIG_HAVE_FTRACE_MCOUNT_RECORD=y
   CONFIG_HAVE_SYSCALL_TRACEPOINTS=y
   CONFIG_HAVE_ARCH_TRACEHOOK=y

   # 功能Trace
   CONFIG_FTRACE=y
   CONFIG_FUNCTION_TRACER=y
   CONFIG_FUNCTION_GRAPH_TRACER=y

   # 打开DYANMIC_FTRACE后性能开销可忽略
   CONFIG_DYNAMIC_FTRACE=y 

   # 各种tracer的支持
   CONFIG_GENERIC_TRACER=y
   CONFIG_NOP_TRACER=y
   CONFIG_STACK_TRACER=y
   CONFIG_IRQSOFF_TRACER=y
   CONFIG_PREEMPT_TRACER=y
   CONFIG_SCHED_TRACER=y
   CONFIG_CONTEXT_SWITCH_TRACER=y
   CONFIG_TRACER_SNAPSHOT=y
   CONFIG_TRACER_SNAPSHOT_PER_CPU_SWAP=y

   # 静态Trace events
   CONFIG_TRACEPOINTS=y
   CONFIG_EVENT_TRACING=y
   CONFIG_TRACING_SUPPORT=y
   CONFIG_TRACING=y

   # 动态Trace events
   CONFIG_PROBE_EVENTS=y
   CONFIG_KPROBE_EVENTS=y
   CONFIG_UPROBE_EVENTS=y

   # 其它相关配置
   CONFIG_TRACE_CLOCK=y
   CONFIG_STACKTRACE_SUPPORT=y
   CONFIG_STACKTRACE=y
   CONFIG_TRACE_IRQFLAGS_SUPPORT=y
   CONFIG_TRACE_IRQFLAGS=y
   CONFIG_SYSCTL_EXCEPTION_TRACE=y
   CONFIG_TRACER_MAX_TRACE=y
   CONFIG_FTRACE_SYSCALLS=y
   CONFIG_BLK_DEV_IO_TRACE=y


3 可挂载的文件系统
------------------

.. code:: c

   # 挂载到debugfs下
   mount -t debugfs debugfs /sys/kernel/debug

   # 挂载到tracefs下
   mount -t tracefs tracefs /sys/kernel/tracing


4 ftrace使用流程
----------------

.. code:: c

   # 1、挂载tracefs文件系统
   mount -t tracefs tracefs /sys/kernel/tracing

   # 2、关闭trace
   echo 0 > tracing_on

   # 3、设置当前要使用的tracer(这里以函数tracer为例)
   echo function_graph > current_tracer

   # 4、设置要跟踪的函数(以do_fork、init_module为例, 可以设置多个)
   echo do_fork > set_ftrace_filter
   echo inix_module > set_ftrace_filter

   # 5、设置函数调用深度
   echo 128 > max_graph_depth

   # 6、开启trace
   echo 1 > tracing_on

   # 7、查看trace
   cat trace          // 静态显示trace, 显示完buffer还在, echo > trace后清空buffer
   cat trace_pipe     // 动态显示trace信息
   snapshot           // 获取当前trace buffer备份
   trace_marker       // 把用户层的log插入trace buffer, 实现用户和内核trace log同步


.. note::

   cat trace_pipe > /tmp/trace.out & 输出日志到文件

5 optioin
---------

5.1 使能函数最终支持栈回溯
**************************

.. code:: c

   echo 1 > options/func_stack_trace

5 其他工具
----------

trace-cmd可以简化命令, 生成trace.data文件提供给 ``kernelShark`` 等UI工具解析, 实现trace的可视化

trace-cmd下载git clone [https://github.com/rostedt/trace-cmd.git](https://github.com/rostedt/trace-cmd.git)

使用
./trace-cmd record -e hbpvt -e sched -e irq

# 参考网站
http://t.zoukankan.com/sky-heaven-p-5321553.html

https://zhuanlan.zhihu.com/p/479833554
