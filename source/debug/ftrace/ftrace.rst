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
