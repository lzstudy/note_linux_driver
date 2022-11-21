USB子系统
===========

1 基本介绍
-----------

USB子系统分为

1.1 重要文件
*************

========== ========================================================================
usb.c      usb core核心文件, usb子系统初始化, usbfs和usbdebugfs初始化, hub初始化等
hcd.c      usb主机框架, 提供注册接口给控制器
udc-core.c usb设备框架, 提供接口给设备
========== ========================================================================

2 重要数据结构
****************

2.1 驱动相关数据结构
*********************

.. code-block:: c

    # USB驱动
    struct usb_driver {
        const char *name;                                                           /**@ 驱动名字. */
        int (*probe) (struct usb_interface *intf, const struct usb_device_id *id);  /**@ probe接口. */
        int (*pre_reset)(struct usb_interface *intf);                               /**@ 预复位. */
        int (*post_reset)(struct usb_interface *intf);                              /**@ 复位后. */
        const struct usb_device_id *id_table;
        struct usbdrv_wrap drvwrap;
    };

    # USB设备
    struct usb_device_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;
        __le16 bcdUSB;
        __u8  bDeviceClass;
        __u8  bDeviceSubClass;
        __u8  bDeviceProtocol;
        __u8  bMaxPacketSize0;
        __le16 idVendor;
        __le16 idProduct;
        __le16 bcdDevice;
        __u8  iManufacturer;
        __u8  iProduct;
        __u8  iSerialNumber;
        __u8  bNumConfigurations;
    }

    struct usb_device {
        int devnum;                                                                 /**@ 设备号, 地址号. */
        enum usb_device_state state;                                                /**@ usb设备状态. */
        enum usb_device_speed speed;                                                /**@ usb速度, 低速, 高速, 全速. */
        struct usb_tt *tt;
        int ttport;                                                                 /**@ tt端口. */
        unsigned int toggle[2];                                                     /**@ [0] - IN, [1] - OUT . */
        struct usb_device *parent;                                                  /**@ hub, 如果是root_hub, 这里为NULL. */
        struct usb_host_endport ep0;                                                /**@ 0号端点. */
        struct usb_device_descriptor descriptor;                                    /**@ USB设备描述符. */
        struct usb_host_config *config;                                             /**@ 所有的主机配置. */
        struct usb_host_config *actconfig;                                          /**@ 当前工作的配置. */
        struct usb_host_endpoint *ep_in[16];                                        /**@ 输入端点. */
        struct usb_host_endpoint *ep_out[16];                                       /**@ 输出端点. */
        u8 portnum;                                                                 /**@ 父端口号, (origin 1). */
        u8 level;                                                                   /**@ 所在级别. */
        u8 devaddr;                                                                 /**@ XHCI通过硬件知道, 其他和devnum一样. */
        char *product;
        char *manufacturer;
        char *serial;
        int maxchild;                                                               /**@ 如果是hub, 他的端口数量. */
        atomic_t urbnum;
    };

    # 控制器驱动
    struct hc_driver {
    }

    # USB控制器设备
    struct usb_hcd {
    }

    # USB设备驱动
    struct usb_udc {
    };

    # USB接口
    struct usb_interface_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;
        __u8  bInterfaceNumber;
        __u8  bAlternateSetting;
        __u8  bNumEndpoints;
        __u8  bInterfaceClass;
        __u8  bInterfaceSubClass;
        __u8  bInterfaceProtocol;
        __u8  iInterface;
    };

    struct usb_host_interface {
        struct usb_interface_descriptor desc;
        struct usb_host_endpoint *endpoint;
    };

    struct usb_interface {
        struct usb_host_interface *altsetting;
        struct usb_host_interface *cur_altsetting;
        unsigned num_altsetting;
        int minor;
        enum usb_interface_condition condition;
        struct device dev;
        struct device *usb_dev;
    };

    # 匹配类型
    struct usb_device_id {
        /* which fields to match against? */
        __u16		match_flags;

        /* Used for product specific matches; range is inclusive */
        __u16		idVendor;
        __u16		idProduct;
        __u16		bcdDevice_lo;
        __u16		bcdDevice_hi;

        /* Used for device class matches */
        __u8		bDeviceClass;
        __u8		bDeviceSubClass;
        __u8		bDeviceProtocol;

        /* Used for interface class matches */
        __u8		bInterfaceClass;
        __u8		bInterfaceSubClass;
        __u8		bInterfaceProtocol;

        /* Used for vendor-specific interface matches */
        __u8		bInterfaceNumber;
        /* not matched against */
        kernel_ulong_t	driver_info
    };  

    # 注册USB驱动(本质为一个接口驱动)
    usb_deregister(struct usb_driver *new_driver);

    # 添加hcd控制器
    usb_add_hcd(struct usb_hcd *hcd, unsigned int irqnum, unsigned long irqflags);

    # 发送可控制消息
    usb_control_msg()

3 USB流程
-----------

USB流程包括

- 初始化流程

2.1 USB系统初始化流程
**********************

#. 初始化USB debugfs
#. 注册USB总线
#. 注册usbfs和device字符设备(主设备号180, 189)
#. 初始化usb hub

2.2 控制器hcd注册初始化流程
*****************************

#. 创建hcd设备usb_create_hcd
#. 注册到内核usb_add_hcd

2.3 设备udc注册流程(uvc)
**************************

.. code-block:: c

    # 1 设置驱动信息
    idVendor, idProduct, bInterfaceClass

    # 2 注册驱动
    usb_register(&uvc_driver.driver) => usb_register_driver => usb_probe_interface


2.4 插入检测流程
******************

.. code-block:: c

    # 1 触发中断
    el1_ir1 => gic_handle_irq => usb_hcd_irq => xhci_irq => hub_irq => hub_event

    # 2 检测hub上所有的端口, 判断是哪个端口的事件
    hub_event => usb_reset_device => 循环查找hud上所有port => port_event(hub, i) => hub_port_connect_change => hub_port_connect

    # 3 检测出哪个端口后, 设备上电, 端口初始化, 设备进入POWER状态
    usb_alloc_dev => USB_STATE_POWERD => hub_port_init
    
    # 4 端口初始化, 复位, 使设备进入DEFAULT状态, 获取设备描述符, 进入地址态 BUS_STATE_ADDRESS
    hub_port_reset => hcd->driver->reset_device(hcd, udev); => usb_set_device_state(udev, USB_STATE_DEFAULT);
    
    # 5 设置端点0速度, 根据udev->speed类型为低速高速, 设置设备地址, 获取设备描述符
    udev->ep0.desc.wMaxPacketSize = xxx => hub_set_address => usb_get_device_descriptor

    # 6 注册USB设备到内核, 探测interface进行匹配具体驱动(按厂商规格匹配、class匹配、接口类匹配)
    usb_new_device => device_add => bus_probe_driver => really_probe => usb_probe_interface

    # 7 根据设备描述符来匹配具体的驱动
    usb_match_id(intf, driver->id_table) => uvc_probe => v4l2_device_register

    # 8 复位usb设备, 配置带宽, 设置状态为配置 USB_STATE_CONFIGURED
    usb_reset_device => usb_reset_and_verify_device => usb_hcd_alloc_bandwidth

    # 9 设置接口
    usb_set_interface, usb_control_msg(dev, 端点, USB_REQ_SET_INTERFACE, USB_RECIP_INTERFACE)





