tracer - function
=================

1 使用流程
----------

.. code:: c

   # 1、挂载tracefs文件系统
   mount -t tracefs tracefs /sys/kernel/tracing

   # 2、关闭trace
   echo 0 > tracing_on

   # 3、设置function tracer(graph更为直观)
   echo function_graph > current_tracer

   # 4、设置要跟踪的函数(以do_fork、init_module为例, 可以设置多个)
   echo do_fork > set_graph_function
   echo inix_module >> set_graph_function

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

   function trace中设置
   * set_ftrace_filter, 用于设置function
   * set_ftrace_pid, function/function_graph 只跟踪某个进程

.. tip::
 
   * 如果被调试的进程运行时间很短, 可使用脚本进行检测

      sh -c "echo $$ > set_ftrace_pid; echo 1 > tracing_on; kill xxx; echo 0 > tracing_on"


   * 如果函数名很多, 且相似, 可以使用正则匹配

      echo 'dev_attr_*' > set_ftrace_filter


   * 追加被跟踪函数

      echo ip_rcv >> set_ftrace_filter

      cat set_ftrace_filter


   * 过滤模块函数<function>:<command>:<parameter>, 过滤ext3模块的write*函数

      echo 'write*:mod:ext3' > set_ftrace_filter


   * 从过滤列表删除某个函数

      echo '!ip_rcv' >> set_ftrace_filter
      
      cat set_ftrace_filter
