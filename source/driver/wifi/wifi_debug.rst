wifi 调试
=========

1 调试交付流程
--------------

- ``iwlist wlan0 scan``  确认wifi能正常工作, 扫描其他设备
- ``wpa_supplicant -v`` 确认wpa功能正常
- 

2 需要移植的工具
----------------

- wireless
- openssl
- wpa_supplicant
- libnl

3 wireless常用命令
------------------

- iwconfig: 设置无线网络相关参数
- iwlist: 扫描当前无线网络信息, 获取WiFi热点
- iwspy: 扫描每个节点链接的质量

3.1 iwlist
**********

.. code:: c

   iwlist wlan0 scan

搜索当前环境下所有的wifi热点, 包括MAC地址, ESSSID(wifi名字), 频率, 速率, 信号质量

4 wpa_supplicant
----------------

4.1 配置wifi相关信息
********************

.. code:: c

   # 打开配置文件
   vi /etc/wap_supplicant.conf

   # 配置内容
   ctrl_interface=/var/run/wpa_supplicant
   ap_scan=1
   network={
      ssid="ZZK"    // 要链接的wifi热点名字
      psk="xxxxxx"  // 要链接的wifi密码
   }

4.2 链接热点
************

.. code:: c

   mkdir /var/run/wpa_supplicant -p
   wpa_supplicant -D wext -c /etc/supplicant.conf -i wlan0 &

4.3 动态获取IP
**************

.. code:: c

   udhcpc -i wlan0
   ifconfig wlan0

   # 使用指定ip地址ping
   ping -I 192.168.1.126 www.baidu.com
