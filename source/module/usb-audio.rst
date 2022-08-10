支持USB声卡
===========

需要加载的KO
------------

::

   insmod snd-rawmidi.ko
   insmod snd-hwdep.ko
   insmod snd-usbmidi-lib.ko
   insmod snd-usb-audio.ko

如何编译上述KO
--------------

::

   Device Drivers --->
       Sound Card Support --->
	       Advaced Linux Sound Architecture --->
		       USB Sound Devices --->
			       <M> USB Audio/MIDI driver

