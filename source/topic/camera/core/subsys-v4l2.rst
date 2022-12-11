子系统
===========

1 系统流程
------------

1.1 注册video节点
***********************

一般video结构会嵌入到priv得结构中

.. code-block:: c

    struct video_device *video;

    video = video_device_alloc();

    # 1 注册v4l2_dev
    video->v4l2_dev = kzalloc(sizeof(*v4l2_dev));
    v4l2_device_register(&pdev->dev, video->v4l2_dev);

    # 2 create ctrl
    v4l2_ctrl_handler_init();

    # 3 注册一系列subdev
    subdev_register

    # 4 注册video device, 其中video_ioctl_ops对应应用层IOCTL
    video->fops = &video_ops;
    video->ioctl_ops = &video_ioctl_ops;
    video_register_device(video, VFL_TYPE_GRABBER, -1);

    # 5 注册subdev
    v4l2_device_register_subdev_nodes(video->v4l2_dev);

2 原理分析
--------------
2.1 reqbuf
***************

=================== =========================== =========================================================
数据结构             父对象                       功能描述
struct video_device                             每个video_device对应/dev/video节点
struct v4l2_device  struct video_device
struct media_device struct v4l2_device
struct v4l2_buffer                              是vb2_buffer的应用形式
struct vb2_buffer   struct vb2_queue            帧缓冲, 里面存放的RAM转换为YUV的数据(转换过程在mem_ops实现)
struct vb2_queue    struct video_device         管理vb2_buffer
=================== =========================== =========================================================

.. code-block:: c

    # 数据结构 - video_device
    struct video_device {
        # meidia相关
        struct media_entity entity;
        struct media_intf_devnode *intf_devnode;
        struct media_pipeline pipe;

        const struct v4l2_file_operations *fops;    // file ops
        u32 device_caps;                            // 设备能力
        struct v4l2_device *v4l2_dev;               // v4l2 device
        struct v4l2_ctrl_handler *ctrl_handler;     // ctrl句柄
        struct vb2_queue *queue;                    // 队列
        char name[32];                              // 名字
        int minor;                                  // 次设备号
        const struct v4l2_ioctl_ops *ioctl_ops;     // ioctl
    };

    # 数据结构 - v4l2_device
    struct v4l2_device {
        struct device *dev;
        struct media_device *mdev;                  // media设备
        struct list_head subdevs;                   // subdev集合
        char name[V4L2_DEVICE_NAME_SIZE];           // 名字
        struct v4l2_ctrl_handler *ctrl_handler;     // ctrl
    };

    # 数据结构 - medida_device
    struct media_device {
        struct device *dev;                         // 父设备(应该是指v4l2_device)
        struct media_devnode *devnode;              // 和/dev/mediax相关
        char model[32];                             // device model名称
        char driver_name[32];                       // 驱动名称
        char serial[40];                            // 序列号
        char bus_info[32];                          // 总线信息
        struct list_head entities;                  // entity链表
        struct list_head interfaces;                // 接口链表
        struct list_head pads;                      // pad链表
        struct list_head links;                     // link链表
        const struct media_device_ops *ops;         // ops接口, 没有太重要的ops
    };

    # 数据结构 - v4l2_plane
    struct v4l2_plane {
        __u32 bytesused;                            // plane数据大小(payload)
        __u32 length;                               // sizeof(this plane)
        union {
            __u32 mem_offset;                       // 用于 V4L2_MEMORY_MMAP(常用)
            unsigned long userptr;                  // 用于 V4L2_MEMORY_USERPTR(一般不用)
            __s32 fd;                               // 用于 V4L2_MEMORY_DMABUF(一般不用)
        } m;
        __u32 data_offset;                          // 一般为0
        __u32 reserved[11];
    };

    # 数据结构 - v4l2_buffer
    struct v4l2_buffer {
        __u32 index;                                // buffer ID
        __u32 type;                                 // buffer类型, 一般为 V4L2_BUF_TYPE_VIDEO_CAPTURE_MPLANE
        __u32 bytesused;                            // payload大小
        __u32 flags;                                // 
        __u32 field;                                // 一般固定filed_none
        struct timeval timestamp;                   // 时间戳
        struct v4l2_timecode timecode;
        __u32			sequence;                   // 
        __u32			memory;                     // 枚举值
        union {
            __u32           offset;                 // 用于non-multiplaner, 偏移地址
            unsigned long   userptr;                // 用于non-multiplaner, 基本地址
            struct v4l2_plane *planes;              // 用于multiplaner
            __s32		fd;
        } m;
        __u32			length;                     // 对于no-planer, 就是数据长度, 对于多planer, 为planer数量
        __u32			reserved2;
        union {
            __s32		request_fd;
            __u32		reserved;
        };
    };

    # 数据结构 - vb2_buffer
    struct vb2_buffer {
        struct vb2_queue *vb2_queue;                // buffer所属队列
        unsigned int index;                         // buffer id
        unsigned int type;                          // buffer 类型
        unsigned int memory;                        // 没用
        unsigned int num_planes;                    // plane数量
        u64	 timestamp;
        struct media_request	*request;
        struct media_request_object	req_obj;
        enum vb2_buffer_state state;                // buffer state
        struct vb2_plane planes[VB2_MAX_PLANES];    // plane 相关
        struct list_head queued_entry;              // 就绪队列
        struct list_head done_entry;                // 完成队列
    };

    # 数据结构体 - vb2_queue
    struct vb2_queue {
        unsigned int type;                          // 固定V4L2_buf_type
        const struct vb2_ops *ops;                  // 给外部提供的接口, 例如queue_setup的设置
        const struct vb2_mem_ops *mem_ops;          // 底层DMA数据相关
        const struct vb2_buf_ops *buf_ops;          // fill_user_buffer, fill_vb2_buffer

        enum dma_data_direction	dma_dir;            // dma方向
        struct vb2_buffer *bufs[VB2_MAX_FRAME];     // bufers管理
        unsigned int num_buffers;                   // vb2 buffer数量

        struct list_head queued_list;               // 当前在用户空间排队的列表
        struct list_head done_list;                 // 就绪的buffer链表, 可以被dequeue到用户空间
        wait_queue_head_t done_wq;                  // 等待队列, 等待buffer从ready态到dequeued
    };

.. code-block:: c

    # [v4l2-ioctl.c] vb2_ioctl_reqbufs = ops->vidioc_reqbufs
    v4l_reqbufs => ops->vidioc_reqbufs(file, fh, p);

    # [videobuf2-v4l2.c] 核心是通过vb2_core_reqbufs来申请内存, call_qoq会回调驱动的queue_setup
    # 通过这里获取buffer, planer数量和大小 
    vb2_ioctl_reqbufs => vb2_verify_memory_type
                      => fill_buf_caps(vdev->queue, &p->capabilities);
                      => vb2_core_reqbufs => call_qop(q, queue_setup, q, &num_buffers, &num_planes, plane_sizes, q->alloc_devs);

    # [videobuf2-core.c]
    # vb2_core_reqbufs => __vb2_queue_alloc => kzalloc
                 

