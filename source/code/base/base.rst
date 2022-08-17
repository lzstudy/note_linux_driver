基础编程
========

驱动保存/获取私有数据
---------------------

- **container_of**

.. code:: c

   struct my_device {
       struct my_priv;
       struct cdev dev;
   };

   # 将来可以使用contanier_of来获取my_device
   container_of(dev, struct my_device, dev)

.. note::

   container_of获取的dev不可以是指针类型, 必须是实体

- **嵌入device的private_data**

struct device中有一个private_data指针, 可以存放私有数据

.. code:: c

   input_set_private();
   input_get_private();

