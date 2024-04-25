## 阻塞IO

阻塞 socket



## 非阻塞IO

非阻塞 socket

```cpp
int socket (int __domain, int __type, int __protocol)

// eg
int fd = socket(PF_INET, SOCK_STREAM | SOCK_NOBLOCK, 0);
```

轮询



## IO 多路复用

select  poll  epoll

* SELECT 
  * 将已连接的 Socket 都放到一个**文件描述符集合**，调用 select 将文件描述符集合**拷贝**到内核里，让内核来**遍历检查**是否有网络事件产生。当检查到有事件产生后，将此 Socket 标记为可读或可写， 接着再把整个文件描述符集合**拷贝**回用户态里，然后用户态还需要再通过**遍历**的方法找到可读或可写的 Socket，然后再对其处理。
  * select 使用固定长度的 BitsMap 默认最大值为 `1024`
  * 两次拷贝两次遍历
* POLL
  * 用动态数组表示，其他并无不同
* EPOLL

```c++
#include <sys/epoll.h>
// 创建 Epoll
int epoll_create(int size)；
// 事件注册函数
// 第一个参数是epoll_create()的返回值，第二个参数表示动作，用三个宏来表示：
// EPOLL_CTL_ADD：注册新的fd到epfd中；
// EPOLL_CTL_MOD：修改已经注册的fd的监听事件；
// EPOLL_CTL_DEL：从epfd中删除一个fd；
//第三个参数是需要监听的fd，第四个参数是告诉内核需要监听什么事，struct epoll_event结构如下：
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
// 
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
// TODO

// eg:
int s = socket(AF_INET, SOCK_STREAM, 0);
bind(s, ...);
listen(s, ...)
int epfd = epoll_create(...);
epoll_ctl(epfd, ...); //将所有需要监听的socket添加到epfd中

while(1) {
    int n = epoll_wait(...);
    for(接收到数据的socket){
        //处理
    }
}
```

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8/epoll.png)

#### ET 边缘触发和 LT 水平触发

- 使用边缘触发模式时，当被监控的 Socket 描述符上有可读事件发生时，**服务器端只会从 epoll_wait 中苏醒一次**，即使进程没有调用 read 函数从内核读取数据，也依然只苏醒一次，因此我们程序**要保证一次性将内核缓冲区的数据读取完**；
- 使用水平触发模式时，当被监控的 Socket 上有可读事件发生时，**服务器端不断地从 epoll_wait 中苏醒，直到内核缓冲区数据被 read 函数读完才结束**，目的是告诉我们有数据需要读取；

#### Reactor

非阻塞同步

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/Reactor/%E4%B8%BB%E4%BB%8EReactor%E5%A4%9A%E7%BA%BF%E7%A8%8B.png)

#### Proactor

异步

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/Reactor/Proactor.png)



- **Reactor 是非阻塞同步网络模式，感知的是就绪可读写事件**。在每次感知到有事件发生（比如可读就绪事件）后，就需要应用进程主动调用 read 方法来完成数据的读取，也就是要应用进程主动将 socket 接收缓存中的数据读到应用进程内存中，这个过程是同步的，读取完数据后应用进程才能处理数据。
- **Proactor 是异步网络模式， 感知的是已完成的读写事件**。在发起异步读写请求时，需要传入数据缓冲区的地址（用来存放结果数据）等信息，这样系统内核才可以自动帮我们把数据的读写工作完成，这里的读写工作全程由操作系统来做，并不需要像 Reactor 那样还需要应用进程主动发起 read/write 来读写数据，操作系统完成读写工作后，就会通知应用进程直接处理数据。



## 信号驱动的IO

## 异步IO