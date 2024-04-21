# LINUX 网络编程

[toc]

## 网络常识

1. #### eth0 eth0:1 eth0.1

eth0 eth0:1 和eth0.1三者的关系对应于**物理网卡**、**子网卡**、**虚拟VLAN网卡**的关系



## Linux 网络相关命令

* arp 查看本地 arp 缓存

* tcpdump
  * Name : dump traffic on a network

* ifconfig

  * brif : 查看主机激活状态的**网络接口情况**； 输出结果中：**lo 是表示主机的回坏地址**，**eth0 表示第一块网卡**， 其中 HWaddr 表示网卡的**物理地址**（MAC地址）； inet addr 用来表示网卡的**IP**地址，Bcast表示**广播地址**，Mask表示**掩码地址**
  * 可以配置网卡的地址、广播地址和掩码
  * 可以配置虚拟网络接口
    * 虚拟网络接口指的是为一个网络接口指定多个IP地址，虚拟接口是这样的 eth0:0 、 eth0:1、eth0:2 ... .. eth1N。当然您为eth1 指定多个IP地址，也就是 eth1:0、eth1:1、eth1:2 ... ...以此类推

  * Lo, 网络配置一定需要有 **环回接口**，也就是loopback。
    * 环回接口可以配置，而且是一个网络号，并非主机号，除非把掩码配置为32bits。在tcp ip详解里面有提及，就是，net/2的实现，会把到不是完全匹配ip的到lo的报文转发，比如lo的配置为127.0.0.1，到lo 127.0.0.2的报文就会转发，如此就不对了。

* Telnet

  * Telnet既是一種**應用層協定**，同時也屬於TCP/IP協定族之一，可以在區網網路與網際網路中以**虛擬終端機的形式遠端控制伺服器**。可以**在本地就能控制服务器**。要开始一个telnet会话，必须输入用户名和密码来登录服务器。Telnet是常用的**远程控制Web服务器**的方法。

  * 经常用于测试网络及端口占用情况

  * Telnet：以**明碼**方式傳輸資料，簡單來說就是所有傳送的資料都是未經過加密，整體安全性相對較低，預設情況下防火牆不會信任Telnet。(不同于 SSH)

  * ```
    $telnet {IP} {cmd} 
    ```

* host

  * 用于 DNS 查询

  * -t Type

    * A
    * CNAME
    * PTR

  * ```
    $ host -t A baidu.com
    baidu.com has address 110.242.68.66
    baidu.com has address 39.156.66.10
    $ host -t PTR 110.242.68.66
    66.68.242.110.in-addr.arpa domain name pointer baidu.com.
    ```

* netstat 
  * -n 展示地址
  * -l listen
  * -t tcp

ARP协议

* 实现网络层到物理地址的转换
* 工作原理：主机广播ARP请求，收到请求的机器回复应答。
* ARP 会在本地维护一个高速缓存
* ARP 技术可以用于**虚拟IP技术** 在高可用领域像数据库SQLSERVER、web服务器等场景下使用很多。同一个物理网卡是可以拥有多个ip地址的，虚拟网卡也可用通过该方式拥有多个ip。 即对外提供数据库服务器的主机除了有一个真实IP外还有一个虚IP，使用这两个IP中的 任意一个都可以连接到这台主机，所有项目中数据库链接一项配置的都是这个虚IP，当服务器发生故障无法对外提供服务时，动态将这个虚IP切换到备用主机。

   

  其实现原理主要是靠**TCP/IP的ARP协议**。因为ip地址只是一个逻辑地址，在以太网中MAC地址才是真正用来进行数据传输的物理地址，每台主机中都有一个**ARP高速缓存**，存储同一个网络内的IP地址与MAC地址的对应关系，以太网中的主机发送数据时会先从这个缓存中查询目标IP对应的MAC地址，会向这个MAC地址发送数据。操作系统会自动维护这个缓存。这就是整个实现 的关键。

## 面试问题

### 大端小端优点

**小端模式**的优点：

1. [内存的低地址处存放低字节，所以在强制转换数据类型时不需要调整字节的内容。例如，把int的4字节强制转换成short的2字节时，就直接把int数据存储的前两个字节给short就行，因为其前两个字节刚好就是最低的两个字节，符合转换逻辑](https://zhuanlan.zhihu.com/p/360037797)[1](https://zhuanlan.zhihu.com/p/360037797)。
2. [CPU做数值运算时从内存中依顺序依次从低位到高位取数据进行运算，直到最后刷新最高位的符号位，这样的运算方式会更高效](https://zhuanlan.zhihu.com/p/360037797)[1](https://zhuanlan.zhihu.com/p/360037797)。

**大端模式**的优点：

1. [符号位在所表示的数据的内存的第一个字节中，便于快速判断数据的正负和大小](https://zhuanlan.zhihu.com/p/360037797)[1](https://zhuanlan.zhihu.com/p/360037797)。

[这两种模式各有各的优点，正因为两者彼此不分伯仲，再加上一些硬件厂商的坚持，因此在多字节存储顺序上始终没有一个统一的标准](https://zhuanlan.zhihu.com/p/360037797)[1](https://zhuanlan.zhihu.com/p/360037797)[。在网络编程和一些服务器中采用的是大端的字节序，而一般的主机采用的是小端的字节序](https://www.zhihu.com/question/25311159)[2](https://www.zhihu.com/question/25311159)[。这两种模式各有其优势，因此都被广泛使用](https://zhuanlan.zhihu.com/p/360037797)[3](https://bing.com/search?q=小端和大端有什么优点)[4](https://zhuanlan.zhihu.com/p/623607570)[5](https://zhuanlan.zhihu.com/p/97821726)。





### API

创建 Socket

```c++
#include <sys/types.h>
#include <sys/sockets.h>
int socket(int domain, int type, int protocol);
int fd = socket(PF_INET, SOCK_STREAM, 0);
```

bind 为socket 命名,绑定一个地址

````c++
bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
````

创建一个 IPv4 socket 地址

```c++
#include <bits/socket.h>
struct sockaddr_in address;
bzeros(address, sizeof(address));
address.sin_family = AF_INET;
inet_pton(AF_INET, ip, &address.sin_addr);
address.sin_port = htons(port);

//
bind(fd, (sockaddr*)&address, sizeof(address));
```

监听 listen

```c++
int listen(int sockfd, int backlog); // 创建监听队列,设置最大连接数
//
listen(fd, 5);
```

接受 accept

```c++
int accept(int sockfd, const struct sockaddr *addr, socklen_t* addrlen);
// 这里的长度是一个指针? 是一个传入传出参数
// make client addr
socklen_t client_addrlength = sizeof(client);
int conn_fd = accept(fd, (struct sockaddr *)&client_addr, *client_addrlength);
// accept 是非阻塞的,只是从监听队列里取一个出来
```

连接  connect

```c++
int connect(int sockfd, const struct sockaddr* serv_addr, skcklen_t addrlen);
//
```

关闭 close

```c++
int close(fd); // 只是引用计数--
int shutdown(int fd, int howto); // 直接关闭,可以指定关闭读/写端
```

设置 socket 选项 getsockopt setsockopt

```c++
// 重用本地地址
int reuse = 1;
setsockopt(fd, SOL_SOCKET, SO_REUSEADDR, &reuse, sizeof(reuse)); 
// SO_KEEPALIVE 发送周期性报文维持连接
// SO_SNDLOWAT 发送低水位 空闲空间大于低水位通知可写
// SO_RCVLOWAT 接收低水位 接收空间大于低水位通知可读
// SO_SNDBUF 发送缓冲区
// SO_RCVBUF 接收缓冲区
```

