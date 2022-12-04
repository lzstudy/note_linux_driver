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

3 USB初始化流程
----------------

USB流程包括

#. USB系统初始化流程
#. USB host框架初始化流程(目前有些框架和host在一起初始化)
#. USB host初始化流程
#. USB gadget框架初始化流程
#. USB device初始化流程

3.1 USB系统初始化流程
**********************

.. code-block:: c

    # 1 注册debugfs
    usb_debugfs_init();
    
    # 2 注册总线通知, 其中usb_bus_type中实现驱动和设备匹配(很关键)
    bus_register_notifier(&usb_bus_type, &usb_bus_nb) => usb_device_match() => usb_match_one_id()

    # 3 注册设备号, 为180, 用于usbfs
    usb_major_init() => register_chrdev(USB_MAJOR, "usb", &usb_fops); => usb_register(usbfs_driver);

    # 4 为usb设备注册主设备号189, 只有纯usb设备才会用, 所以大部分场景不会用
    usb_devio_init() => register_chrdev_region(USB_DEVICE_DEV, USB_DEVICE_MAX, "usb_device");

    # 5 hub初始化, 注册hub驱动, 申请hub工作队列, 注意这里只是申请队列头, 没有具体事件
    usb_hub_init() => usb_register(&hub_driver) => alloc_workqueue("usb_hub_wq", WQ_FREEZABLE, 0);

    # 6 注册USB通用驱动, 里面实现了usb选择配置的过程, 设置配置过程中有关于带宽的设置(很重要, 建议加入ftrace调试)
    usb_register_device_driver(&usb_generic_driver, THIS_MODULE); | generic_probe => usb_choose_configuration => usb_set_configuration

3.2 USB host框架初始化流程
*****************************

============================== ================
kernel/drivers/usb/dwc2/hcd.c  host框架控制接口
drivers/usb/chipidea/host.c    host设备接口
============================== ================

USB host框架使用hcd.c中得接口无需初始化
目前并不统一, 所以有多个框架, 对应不同的目录, 并且有的框架和host是一起初始化的

3.3 USB Host初始化流程
************************

============================== ================
kernel/drivers/usb/dwc2/hcd.c  host框架控制接口
drivers/usb/chipidea/host.c    host设备接口
============================== ================

#. dw2
#. dw3
#. chipidea, imx6ull用的此框架(chipidea是usb芯片公司)

.. code-block:: c

    # 1 获取设备信息, 初始化usb phy
    ci_hdrc_probe => hw_device_init() => ci_usb_phy_init()

    # 2 初始化中断, 判断自己是host或device
    platform_get_irq() => ci_hdrc_host_init() / ci_hdrc_gadget_init()

    # 3 host初始化
    ci_hdrc_host_init() | ops && host_start && host_irq

    # 4 初始化角色 回调3得host_start
    ci_get_role(ci); && ci_role_start() => ci->roles[role]->start(ci) => host_start

    # 5 创建HCD, 初始化urb下半部(将来usb中断从这里过 usb_giveback_urb_bh) 
    usb_create_hcd() && usb_add_hcd() => init_giveback_urb_bh() => tasklet_init(usb_giveback_urb_bh);

    # 6 注册中断
    devm_request_irq(dev, ci->irq, ci_irq, IRQF_SHARED, ci->platdata->name, ci);

    # 7 创建power任务
    INIT_WORK(&ci->power_lost_work, ci_power_lost_work);

    # 8 创建debugfs
    ret = dbg_create_files(ci);

.. note:: 
    
    中断函数分析:
    ci_irq => ci_otg_fsm_irq => ci_otg_queue_work

3.4 USB Gadgat框架初始化流程
****************************


3.5 设备udc注册流程(uvc)
**************************

.. code-block:: c

    # 1 设置驱动信息
    idVendor, idProduct, bInterfaceClass

    # 2 注册驱动
    usb_register(&uvc_driver.driver) => usb_register_driver => usb_probe_interface


4 usb事件流程
--------------

4.1 插入检测流程
******************

============ ===========================
hub_irq      进行bt可以分析中断生成过程
============ ===========================

.. code-block:: c

    # 1 触发中断
    el1_ir1 => gic_handle_irq => usb_hcd_irq => xhci_irq => hub_irq => kick_hub_wq => queue_work(hub_wq, &hub->events) => hub_event

    # 2 检测hub上所有的端口, 判断是哪个端口的事件
    hub_event => usb_reset_device => 循环查找hud上所有port => port_event(hub, i) => hub_port_connect_change => hub_port_connect

    # 3 检测出哪个端口后, 设备上电, 端口初始化, 设备进入POWER状态
    usb_alloc_dev => USB_STATE_POWERD => hub_port_init => "%s %s USB device number %d using %s\n"
    
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





