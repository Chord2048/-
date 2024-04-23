[toc]



## 基础语法

#### 指针和引用的区别

指针是变量，存一个地址。引用是一个别名。

指针在传参的时候是值传递，引用是引用传递。

引用必须初始化，指针可以为空，也可以随便指向一个地址。

引用不可以再改变。引用不能为空。

递归的时候用引用可以降低开销。



#### 堆和栈的区别

1. 大小、位置不同
   1. 栈空间比较小，向低地址增长。申请的地址是固定的。
   2. 堆空间比较大，向高地址增长。申请的位置可以变化。
2. 申请和管理方式不同
   1. 栈是系统自动分配的。自动回收。
   2. 堆要自己手动申请。由内存泄漏风险。
3. 申请效率不同
   1. 栈由系统分配，快且没有碎片。
   2. 堆由程序员分配，慢且会有碎片。
4. **取栈里的对象要快一些**，因为
   1. 寄存器里由栈地址
   2. 获取堆的内容要先读指针的内容，再读地址的内容。

#### new / delete 与 malloc / free 的异同

* 前者是 C++ 的关键字，调用 new 运算符，后者是 C/c++ 标准库函数。
* 前者自动算大小
* 前者会返回类型，是类型安全的。
* 前者会调用构造函数/析构函数
* 前者可以重载

new 会调用 operator new 申请空间，然后调用构造函数。

##### 重载`operator new`

```c++
class Foo {
public:
 void* operator new(std::size_t size, void* ptr)		// 只要保证第一个参数是 size_t
 {
     std::cout << "placement new" << std::endl;
  return ptr;
 }
}
int main()
{
 Foo* m = new Foo;
 Foo* m2 = new(m) Foo;	// 使用的时候传一个参数给 new
 std::cout << sizeof(m) << std::endl;
    // delete m2;
 delete m;
 return 0; 
}
```

可以用再内存池，不用重新申请空间，而是返回一个已经分配好空间的首地址。

##### 重载`operator delete`

一般不会重载 operator delete，原因是重载后的 operator delete **不能手动调用**。

这种重载的意义是**和重载`operator new`配套**。只有`operator new`报异常了，就会调用对应的`operator delete`。若没有对应的`operator delete`，则无法释放内存。

#### 不同类型的new

* plain new

  * ```c++
    void* operator new(std::size_t) throw(std::bad_alloc); // 会抛出 std::bad_alloc
    void operator delete(void *) throw();
    ```

* nothrow new

  * ```c++
    void * operator new(std::size_t,const std::nothrow_t&) throw(); // 失败时不抛出异常而是返回 Null
    void operator delete(void*) throw();
    ```

* placement new

  * ```
    void* operator new(size_t,void*);	// 不会分配内存，也就不会失败了
    void operator delete(void*,void*);
    ```

#### delete p、 delete [] p、 allocator 都有什么作用？

* delete [] 时，数组中的元素按照逆序进行销毁。
* delete p会调用一次析构函数，而delete[] p会**调用每个成员的析构函数**。
* delete[] 时候会**向前找4个字节获取长度**，这4个字节是未定义的，所以调用了**不固定次数**的析构函数
* allocator 将**内存分配和对象构造分开**，allocator 申请一部分内存，不进行初始化对象，只有需要的时候才会进行初始化操作。

#### malloc 和 free 是怎么实现的？

用系统调用 brk, mmap, munmap 这些系统带哦用实现。

* brk 是堆顶指针向高地址移动
* mmap 是在进程的虚拟空间中（文件映射区）找一快空闲的虚拟内存。
* 在第一次访问的时候，发生缺页中断，操作系统负责分配物理内存，然后简历虚拟内存和物理内存之间的映射关系。
* malloc**大于128k的内存**，使用mmap分配内存，在堆和栈之间找一块空闲内存分配(对应独立内存，而且初始化为0)，
* brk 分配的内存要等到高地址内存释放后才能释放，mmap可以单独释放。当高地址空间的空闲内存高于 128 k 执行内存紧缩。
* 操作系统有一个记录空闲地址的链表，当操作系统收到程序的申请就会遍历链表找到第一个大于申请空间的接待你，然后删除这个节点。

brk 找K线链表的策略：

* 最优匹配：找到 >= M 的最小的节点
* 最差匹配：找到 >= M 的最大的节点
* 首次匹配
* 下次匹配

除了空闲链表的其他空闲内存方式：

* 分离分散链表：每一种大小的空间简历独立的链表

* 伙伴系统：空闲空间递归一分为二直到满足。伙伴系统的伙伴只有1位不同，比较好找。



#### malloc realloc calloc

```c++
void* malloc(unsigned int num_size);
int *p = malloc(20*sizeof(int)); // 申请20个int类型的空间；

void* calloc(size_t n,size_t size);
int *p = calloc(20, sizeof(int)); 	 // 省去计算，并且初始化为 0  

void realloc(void *p, size_t new_size); // 接收一个指针，在其后扩容。主要用于动态扩容。

```





#### 顶层const 底层const

顶层 const 修饰的**变量本身**是一个常量

底层 const 指的是 const 修饰的变量**指向的对象**是一个常量

#### final

禁止继承

禁止重写，C++中还允许将方法标记为fianal，这意味着无法再子类中重写该方法。这时final关键字至于方法参数列表后面，如下

#### 野指针和悬空指针

* 野指针：没有被初始化的指针 ==》 初始化
* 悬空指针：指针最初指向的内存被释放了  ==》 释放后立即置空

#### 重载重写和隐藏

* 重载 overload
  * 同名函数，参数不同
* 重写 override
  * 派生类覆盖基类的同名函数
  * 相同的参数个数、参数类型和返回值类型
* 隐藏
  * 派生类的函数屏蔽了基类的同名函数（可以用：：访问被隐藏的函数）
  * 参数相同，但是基类函数不是虚函数
  * **参数不同，无论基类函数是不是虚函数都会被隐藏**

#### 构造函数的类别

* 默认构造函数

* 初始化构造函数

* 拷贝构造函数

  * ```c++
    Student (const Student&);
    ```

* 移动构造函数

  * ```c++
    Student (Student&&);
    ```

* 委托构造函数

  * 被委托的构造函数在委托构造函数的初始化列表里被调用，而不是在委托构造函数的函数体里被调用。

* 转换构造函数

  * 只有一个其他类型的形参

#### 类成员初始化？构造函数顺序？初始化列表为什么快？

* 赋值初始化(在{}里初始化) 是先分配内存空间才初始化。

* 列表初始化时给数据成员分配空间的时候就初始化。初始化的时候函数体还没执行

派生类构造函数的执行顺序

1. 虚基类
2. 基类
3. 类类型成员的构造函数
4. 自己的构造函数

前者是构造函数里赋值，后者是纯粹的初始化操作。赋值操作有时候会产生临时对象。

#### 什么时候必须成员列表初始化？作用是什么？

其实就是什么时候不能用赋值初始化。

1. 引用成员
2. 常量成员
3. 基类带参数的构造函数
4. 类成员的带参数的构造函数

列表初始化实际上：

1. 编译器在构造函数内安插初始化操作。
2. 初始化顺序和声明顺序相关。







#### 浅拷贝和深拷贝

* 浅拷贝：只拷贝一个指针，不开辟新的地址
* 深拷贝：拷贝指针值，并且开辟出新的空间

#### 大端和小端

* 大端：高字节在低地址
* 小端：低字节在低地址



#### volatile mutable explicit

* volaile
  * 表示变量可以被编译器未知因素更改（OS, Thread, hardware）
  * 编译器对访问该变量的代码不在进行优化
  * 总是重新从它所在的地址读取数据
  * **防止编译器把值放入寄存器**
* mutable
  * 意思是可变的，和 const 是反义词
  * 有些时候可能想在 const 函数里修改一些跟状态无关的数据成员
* explicit
  * 不能发生隐式类型转换
  * 只能加在构造函数声明上
  * 被 explicit 修饰的构造函数的类不能发生隐式类型转换

#### 异常处理

#### try throw catch

* catch(...) 可以捕获任何异常

* catch 的异常不想在本函数处理，可以在 catch 里抛出异常。

* 异常声明：

  * ```c++
    int fun() throw(int,double,A,B,C){...}; // throw 里声明能抛出的异常的列表
    ```

* 标准异常 exception

  * std::bad_typeid
  * std::bad_cast
  * std::bad_alloc
  * ...
  * ![C++ 异常的层次结构](https://www.runoob.com/wp-content/uploads/2015/05/exceptions_in_cpp.png)
  * 自定义异常
    * 方法：继承和重载 excepption 类

#### static

1. 隐藏在文件作用域
   1. 函数默认是 extern 声明的
   2. 定义静态函数可以在其他文件定义同名函数，并且不会被其他文件引用
2. 保持内容的持久，存储在静态存储区
3. **static 类对象必须在类外初始化**
   1. static 修饰的对象先于对象存在，因此要在类外初始化
4. static 对象不属于任何对象或者实例
   1. 因此不能被 virtual 修饰



#### main 函数之前做了什么事情？

* 设置栈指针
* 初始化 static 对象和 global 对象，也就是 .data 段的内容
* 将未初始化的全局变量赋予初值
* 全局对象初始化，也就是调用构造函数。（可以注入一些代码在 main 之前执行）
* 将 main 函数的参数 argc， argv 传递给 main 函数。

#### main 函数执行完之后呢？

* **全局对象的析构**

#### 野指针 悬空指针

野指针指向未知的区域

* 指针没有初始化

悬空指针

* 指针指向的内容已经被释放了。
* 或者声明周期已经结束了。

解决：

* 初始化
* 用指针的时候判断是不是空的
* 释放之后指针置为 nullptr
* 使用智能指针

#### 什么是内存泄漏

内存泄漏：分配的内存没有释放，导致这块内存不能被再次使用。

原因：

* new 了**没有 delete** 或者**没有 delete []**。
* 析构函数没有释放内存。
* 没有将**基类的析构函数没有声明为虚函数**
  * 否则 delete 派生类的基类指针的时候**派生类的析构函数被覆盖**不能正常析构。

> 《Effective C++》中的观点是，只要一个类有可能会被其它类所继承， 就应该声明虚析构函数。

* 有指针成员，但是**没有自己的拷贝构造函数 / 重载赋值运算符**。
* 返回值为野指针。
* 循环引用。

避免内存泄漏的方法：

* 引用计数法 类似于智能指针
* 在构造的时候 new，析构的时候 delete
* **将基类的虚函数声明为虚函数**
* 对象数组的释放用 **delete []** 也就是 new new[] delete delete[]配套
* 有 new 就别忘了 delete

检测工具

* Valgrind
* Asam

#### 面向对象三大特性

继承多态和封装

多态的方式：

* 覆盖：子类重写父类的虚函数。// 运行时多态
* 重载：允许同名函数，不同参数。// 编译时多态
* 模板，模板特化

#### 四种强制类型转换

* reinterpret_cast<typeid> (exp)
  * 直接转
* const_cast<typeid> (exp)
  * 修改类型的 const 或者 volatile 属性
* static_cast<typeid> (exp)
  * **没有类型检查**，用于基类和派生类之间的转换
    * 上行 把派生类指针/引用换成基类的 ： 安全
    * 下行 把基类的指针/引用换成派生类 ： 不安全
  * 用于基本类型的转换
  * 空指针换成其他类型指针
* dynamic_cast<typeid> (exp)
  * **有类型检查**，基类向派生类转换比较安全
  * 在执行期的时候决定真正的类型。
  * 上行转换和 static_cast 一样
  * 下行转换时 dynamic_cast 有类型检查的功能
    * **dynamic_cast 会给出 nullptr**
    * **而 static_cast 会给出未定义！**





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





## 异步 Promise future packaged_task async 

![std::future、std::promise、std::packaged_task 與 std::async 的關聯圖](https://zh-blog.logan.tw/static/images/2021/09/26/future-class-diagram.png)



## C++协程

#### 关键字

co_await 调用一个 awaiter 对象

co_yield 挂起一个协程

co_return 协程返回

