理论基础
============

1 overview
-------------

================ =======================================
构建工具          说明
busybox           构建过程简单, 只能构建最基本的文件系统
buildroot         可以构造复杂的文件系统, 目前常用
yocto             可以构造复杂的根文件系统, 过程复杂
================ =======================================


2 根文件系统基本目录
-----------------------

============== ======= ================================================================
目录            空目录   说明
/bin                    系统基础命令, 此目录下的命令所有用户可用, 比如ls、mv等命令
/sbin                   系统管理员才能使用
/etc                    系统配置
/lib                    linux运行必须的库文件
/usr                    usr不是user缩写, unix software resource缩写, unix操作系统资源
/sys            Y       sysfs文件系统挂载点
/dev            Y       设备文件目录
/mnt            Y       挂在点
/proc           Y       proc是进程目录
============== ======= ================================================================