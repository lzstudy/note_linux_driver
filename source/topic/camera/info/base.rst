基础知识
==========

1 术语
--------

============== =============================================== =========================================================
缩写/术语      全写                                            描述
AE             auto exposure                                   自动曝光
AF             auto focus                                      自动涂胶
AFM            auto focus measurement                          自动对焦测量
AWB            auto white balance                              自动白平衡
IE             image effects module                            图像效果模块
LSC            lens shade correction                           镜头阴影矫正
MI             memory interface                                内存接口
WDR            wide dynamic range                              宽动态范围
3A             auto exposure, auto focus, auto white balance   自动曝光, 自动对焦, 自动白平衡 
BLC            black level correction                          暗电流校正, 指在没有光源的情况下, 由电子元器件流动的电流
VVCAM          vivante's kernel driver integration layer       vivant内核驱动集成层(vivante: 图芯科技, 美国加州)
MIPI           mobile industry processor interface             移动产业处理器接口
CCI            camera control interface                        控制接口, 一般为i2c或i3c
YUV                                                            Y - 灰度, U - 蓝色分量, V - 红色分量, (绿色分类由Y-UV计算)
LUT            look up table                                   对照表
============== =============================================== =========================================================

2 讲解
---------

摄像头主要有如下知识点

- 摄像头框架: v4l2 和 media
- 控制器端驱动: csi、isp、i2c + dma(imx8mp为dwe模块) 
- 设备端驱动: i2c
- 设备树配置: i2c、pinctrl、csi、isp、dma(imx8mp为dwe)
