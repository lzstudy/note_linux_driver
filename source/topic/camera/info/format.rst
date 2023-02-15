图像参数
=========

1 图像格式
-------------

图像格式指的是存储一张图像使用的存储方法, 分为有损和无损存储

- 无损格式 :
- 有损格式 :

======= =============================================================================================================
格式     说明
RGB      RGB颜色是自然界原始颜色, 其他格式显示最终都要转换为RGB颜色
YUV      为了兼容黑白显示场景的图像, 使用此格式, YUV理论上与RGB是一个级别的, 是另外一种颜色表示方式
RAW      将图像传感器捕捉到的光源转换为数字信号, 一般采用Bayer排列方式, 理论上RAW也属于压缩格式, 最终需要转为RGB格式
JPEG
======= =============================================================================================================

1.1 RGB格式
************

常用格式RGBA8888, RGB888, RGB565, RGB555

1.2 YUV格式
*************

YUV是一种颜色编码方法, RGB也是一种颜色编码方法, Y表示亮度, UV称为色度, 色度可以描述色彩和饱和度

- Y表示亮度Luma, 既灰度, 只使用Y就是一张黑白图像
- U表示蓝色分量, 也叫Cb
- V表示红色分量, 也叫Cr

.. tip:: 
    
    YUV的好处:

    - YUV只需要Y信号, 可以直接给黑白电视或其他只需要黑白显示场景使用
    - 人眼对UV的敏感度小, 适当减少UV不影响感官, 这样可以减少文件大小, 因此有了YUV420、YUV422等格式
    - YUV格式比RGB存储空间小
    - 不过人类所有的显示设备都是RGB格式, YUV最终也需要转为RGB显示(通过插值算法)

.. note::

    YUV的采样方式:

    - YUV444 每1个像素包含自己的Y/U/V
    - YUV422 每2个像素共享一组U/V, 每个像素拥有自己的Y参数
    - YUV420 每4个像素共享一组U/V, 每个像素拥有自己的Y参数

.. note:: 

    YUV的排列方式:

    - planar排列, 把Y, U, V三种数据放到不同的三个内存中
    - semi-plannar排列, Y单独一块内存, UV数据连续交错放在一起(此时UV又有先后之说)
    - packed排列, YUV数据放在一块内存

.. note:: 
    
    YUV的命名规则:

    - YUVxxxP, YUVxxxSP, 其中P就是plannar, SP就是semi-plannar
    - NVxx, YVxx, 例如NV12, NV就是semi-plannar, 12代表一个像素12位, NV12本质就是YUV420SP
    - Ixxx, I420就是YUV420P, I就是p的别名, 此命名方法比较少用

.. note::

    YUV像素位数计算:

    正常Y/U/V和RGB一样, 每个都是8位
    
    - YUV444, Y + U + V = 8 + 8 + 8 = 32位
    - YUV422, Y + 1/2(U + V) = 8 + 1/2(16) = 16位
    - YUV420, y + 1/4(U + V) = 8 + 1/4(16) = 12位 

.. code-block:: c
    
    # YUV子类型及排列规则

    ### YU12/I420  
    yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy     (w*h)
    uuuuuuuu uuuuuuuu
    vvvvvvvv vvvvvvvv

    ### NV12, 先存Y, 然后UV交替, U在前, V在后
    yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy     (w*h)
    uvuvuvuv uvuvuvuv uvuvuvuv uvuvuvuv                                         (w*h/2)

    ### NV21, 先存Y, 然后VU交替, V在前, U在后
    yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy yyyyyyyy     (w*h)
    uvuvuvuv uvuvuvuv uvuvuvuv uvuvuvuv                                         (w*h/2)



1.3 RAW格式
************

raw格式是图像传感器将光信号转为数字信号的图像格式, 一般图像物理上的滤光片采用bayer阵列.
bayer数据 = 50%绿色信息 + 25%的红色 + 和%25的蓝色. Bayer排列有如下四种方式.

.. code-block::

    bayer类传感器滤光片四种物理排列方式:

        |R|G|  |B|G|  |G|R|  |G|B| 
        |G|B|  |G|R|  |B|G|  |R|G|

        RGGB   BGGR   GRBG   GBRG


2 色彩空间
-----------

目前色彩看空间只有SRGB和adobeRGB两种

- SRGB平时使用的较多
- adobeRGB可以显示更为高端的彩色, 一般用于商业打印或出版




