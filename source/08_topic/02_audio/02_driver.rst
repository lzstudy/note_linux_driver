驱动编写
------------------

1 设备树配置
*****************

1.1 i2s节点配置
*********************



1.2 i2c节点配置
**********************

2 驱动编写

3 内核支持
-------------------

::
   -> Device Drivers 
     -> Sound card support (SOUND [=y]) 
       -> Advanced Linux Sound Architecture (SND [=y]) 
         -> ALSA for SoC audio support (SND_SOC [=y]) 
           -> SoC Audio for Freescale CPUs
             -> <*> Asynchronous Sample Rate Converter (ASRC) module support   //选中, 体系架构相关
               -> <*> SoC Audio support for i.MX boards with wm8960            //选中, 具体驱动相关