内核定时器
===============

1 基础理论
----------------

    linux内核使用全局变量jiffies记录从系统启动以来的节拍数, 启动时将jiffies初始化为0, 
    该变量定义在 ``include/linux/jiffies.h``

2 相关接口
----------------

2.1 时间转换接口
****************************
================================ ============================
jiffies_to_mesecs(jiffies);       jiffies转ms
jiffies_to_usecs(jiffies);        jiffies转us
jiffies_to_nsecs(jiffies);        jiffies转ns
mesecs_to_jiffies(ms);            ms转jiffies
usecs_to_jiffies(us);             us转jiffies
nsecs_to_jiffies(ns);             ns转jiffies
================================ ============================

2.2 内核定时器接口
********************
======================== ==========================
init_timer(timer);       初始化定时器
add_timer(timer);        添加定时器
del_timer(timer);        删除定时器
del_timer_sync(timer);   异步删除定时器
mod_timer(timer);        修改定时器
======================== ==========================

2.3 短延时函数
*******************
==================== ================
ndelay(ns);           ns延时
udelay(us);           us延时
mdelay(ms);           ms延时
==================== ================