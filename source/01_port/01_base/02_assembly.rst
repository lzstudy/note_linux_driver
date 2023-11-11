汇编编程
==============

============ ========================== ===========================
DR           data register              数据寄存器
============ ========================== ===========================

1 数据传输指令
----------------

.. code-block:: shell

    # 普通数据转移指令
    mov r0, r1              @ 将r1数据保存到r0中
    mov r0, #0x21           @ 将立即数0x21保存到r0中

    # 特殊寄存器处理
    mrs r0, cpsr            @ 将特殊寄存器保存到r0
    msr cpsr, r0            @ 将r0复制到cpsr中

2 存储器访问
----------------

    arm处理器无法直接访问存储器(ram)中的数据, 需要先保存地址, 然后再读取具体数据

.. code-block:: shell

    # 读取寄存器的值
    ldr r0, =0x0209C004     @ 保存地址到r0
    ldr r1, [r0]            @ 读取地址中的值到r1

    # 将数据写入存储器
    ldr r0, =0x0209C004     @ 保存地址到r0, 既r0 = 0x0209C004
    ldr r1, =0x20000002     @ 保存地址到r1, 既r1 = 0x20000002 
    str r1, [r0]            @ 将r1中的值写入到r0所保存的地址中

3 压栈和出栈
----------------

.. code-block:: shell

    # 压栈操作
    push {r0~r3,r12}        @ 将r0~r3和r12压栈
    push {lr}               @ 将lr进行压栈
    
    # 出栈操作
    pop {lr}                @ 将lr出栈
    pop {r0~r3,r12}         @ 恢复r0~r3和r12

4 跳转指令
----------------

.. code-block:: shell

    b main                  @ 跳转到main
    bl _label               @ 带链接跳转(跳转前将pc保存到lr)

5 算术运算
----------------

.. code-block:: shell

    add r1,r2,r3            @ r1 = r2 + r3
    sub r1,r2,r3            @ r1 = r2 - r3
    mul r1,r2,r3            @ r1 = r2 * r3
    udiv r1,r2,r3           @ r1 = r2 / r3

6 逻辑运算符
----------------

.. code-block:: shell

    # 与操作
    and r1,r2               @ r1 = r1 & r2
    and r1,r2,#immed        @ r1 = r2 & #immed
    and r1,r2,r3            @ r1 = r2 & r3

    # 或操作
    orr r1,r2               @ r1 = r1 | r2
    orr r1,r2,#immed        @ r1 = r2 | #immed
    orr r1,r2,r3            @ r1 = r2 | r3    

    # 位清除
    bic r1,r2               @ r1 = r1 & (~r2)
    bic r1,r2,#immed        @ r1 = r2 & (~#immed)
    bic r1,r2,r3            @ r1 = r2 & (~r3)
