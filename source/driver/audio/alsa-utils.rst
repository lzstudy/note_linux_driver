音频调试
========

音频调试基于ALSA系统, 此笔记主要描述alsa-utlis如何使用

混音器设置[alsamixer]
---------------------

声卡配置工具

.. note::

   - 上 音量加
   - 下 音量减
   - q 左声道音量加
   - z 左声道音量减
   - e 右声道音量加
   - c 右声道音量减


alsa控制[alsactl]
-----------------

配置好声卡后，直接关机后配置会消失, 因此通过此工具保存

- 保存配置

.. code-block:: bash

   alsactl -f /var/lib/alsa/asound.state store

- 加载配置

.. code-block:: shell

   alsactl -f /var/lib/alsa/asound.state restore


录音工具[arecord]
-----------------

- 录制一段音频

::

   arecord -D hw:0,0 -f cd -d 10 test.wav
   arecore -D hw:0,0 -r 16000 -c 1 -f S16_LE test.wav

.. note::

   - -r 设置采样率
   - -c 设置通道数量
   - -D 指定硬件设备, hw:<card>,<device>
   - -f 指定音频格式, cd(16位, 小端, 44100, stereo), cdr(16位, 大端, 44100, stereo), S16_LE表示有符号小端
   - -d 指定录制时长

播放工具[aplay]
---------------

- 播放一段音频

::

   aplay -Dhw:0,0 test.wav

.. note::

   - aplay不需要指定过多参数, 因为wav文件头会指定相关参数, aplay会识别
   - 有时aplay -D hw:0,0 xxx.wav播放不出音乐.先执行aplay xxx.wav来播放, 然后再aplay -D hw:0,0播放

其他功能
--------

- 修改默认播放器

::

   vi /etc/asound.conf

   修改为如下内容
   defaults.pcm.card 1
   defaults.pcm.device 1
   defaults.ctl.card 1

- 查看声卡

::

   cat /proc/asound/cards 				# 比较直观的观察声卡信息

   
