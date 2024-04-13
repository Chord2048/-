[toc]

## 编译连接

* 预处理 g++ **-E** main.cpp -o **main.i**
  * 删除注释
  * 引入头文件 #pragma once once
  * 宏展开

* 编译 g++ **-S** main.i -o **main.s**
  * 代码优化 指令重排？
  * 汇总所有的符号
    * 函数名修饰 (重载)

* 汇编 二进制可重定位文件 **main.o** 每个都有 text data bss heap 内核段，需要合并（链接）
  * 为什么合并？1. 浪费空间 2. 空间局部性不好
  * 汇编编程机器码

* 链接 可执行文件
  * **合并所有的 obj 文件的段**，**调整段的偏移和段长度**，**合并符号表**
  * 地址与空间分配
  * 符号解析与重定位


`.bss`节在目标文件和可执行文件中不占用文件的空间，但是它在装载时占用地址空间

## 智能指针相关

#### 1. enable_shared_from_this

允许一个类继承自它，以便获得指向 `this` 的 `shared_ptr` 

用处：异步回调，事件处理，观察者模式



## 调试相关

GDB 使用

内存泄漏

#### COREDUMP 调试

终止时产生 Coredump 文件，默认为 core 可以 echo “pattern” core_pattern 更改命名规则。



死锁？发个 kill -3 pid 或者 kill -s SIGQUIT pid 产生 core

然后 

```
gdb -c ./a.out ./core
gdb bt	// backtrace 查看调用栈
或者
gdb info stack // 显示变量信息

或者
pstack pid 看进程信息
```



#### 多线程调试

