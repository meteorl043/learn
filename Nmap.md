#Nmap 学习

## Nmap简介
+ 主机发现功能
+ 端口扫描功能
+ 服务及版本检测
+ 操作系统检测
    
> 除了这些基本的功能，还包括一些高级审计技术
> 例如:伪造发起扫描端的身份,进行隐蔽扫描，规避目标的安全防御设备，对系统
进行安全漏洞检测并提供完整的报告选项。
> 随着NSE脚本引擎的推出，任何人都可以向Nmap添加新模块

## Nmap的下载和安装
> 官网：https://Nmap.org/download.html
### 在Windows系统下下载和安装
### 在Linux系统下下载和安装(以Ubantu为例)
1. 首先下载RPM软件包管理器:`sudo apt-get install rpm`
2. 之后输入以下命令
    ```bash
    rpm -vhU https://Nmap.org/dist/Nmap-7.25BETA2-1.x86_64.rpm
    rpm -vhU https://Nmap.org/dist/zeNmap-7.25BETA2-1.noarch.rpm
    rpm -vhU https://Nmap.org/dist/ncat-7.25BETA2-1.x86_64.rpm
    rpm -vhU https://Nmap.org/dist/nping-0.7.25BETA2-1.x86_64.rpm
    ```
    > 注意：图形化的Nmap版本Zenmap采用了noarch格式
    
### Nmap的基本操作
最简单的操作就是直接输入Nmap和<目标IP地址>

`Nmap 192.168.0.1`

扫描结果
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-07 20:51 PDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000014s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
53/tcp   open  domain
111/tcp  open  rpcbind
8888/tcp open  sun-answerbook

Nmap done: 1 IP address (1 host up) scanned in 0.20 seconds
```
其中

1. 给出了当前的版本号:7.80 以及扫描时间
2. 是一个标题，关于127.0.0.1的扫描报告
3. 给出目标主机的状态：up(意味着这个主机处于开机并连上互联网的状态)
4. 表示在进行检查的1000个端口中，有997个处于关闭状态
5. 是一张表，有三个字段，分别是PORT，STATE，SERVICE表示了端口号，状态以及运行的服务
6. 如果可能，会给出目标主机的硬件地址，例如:`D8:FE:E3:B3:87:A9`
7. 最后给出了扫描的主机数目以及消耗时间

### 扫描范围的确定
那么如何对多个地址范围进行扫描呢？
##### 对连续范围的主机进行扫描

`Nmap [IP地址范围]`

例如

`Nmap -sn 127.0.0.1-255`

扫描结果
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-07 20:59 PDT
Nmap scan report for 114.214.241.254
Host is up (0.13s latency).
MAC Address: 5C:DD:70:91:72:E2 (Hangzhou H3C Technologies, Limited)
Nmap scan report for 114.214.241.161
Host is up.
Nmap done: 255 IP addresses (2 hosts up) scanned in 20.44 seconds
```
> sn参数随后介绍

##### 对整个子网进行扫描
Nmap支持使用CIDR(Classless Inter-Domain Routing, 无类别域间路由)

`Nmap [IP地址/掩码位数]`

例如

`Nmap -sn 127.0.0.1/24`

扫描结果同上

##### 对多个不连续的主机进行扫描
Nmap可以一次扫描多个主机，如果这些主机没有任何关系，可以使用空格将这些目标地址分隔

`Nmap [扫描目标1 扫描目标2 … 扫描目标n]`

例如

`Nmap 127.0.0.1 127.0.0.2 192.168.0.1`

##### 在扫描的时候排除一些目标

`Nmap [目标] --exclude [目标]`

例如，我们希望扫描整个子网，但不扫描127.0.0.2时

`Nmap 127.0.0.1/24 --exclude 127.0.0.2`

##### 对一个文本文件的地址列表进行扫描

假设如果你要对一个文本文件进行扫描，譬如
```
192.168.0.1
192.168.1.25
192.168.0.168
127.0.0.1
```

可以使用以下命令

`Nmap -iL [文本文件]`

例如

`Nmap -sn -iL list`

##### 随机确定扫描目标

`Nmap -iR [目标的数量]`

例如

`Nmap -sn -iR 3`

## 活跃主机发现技术

> 活跃主机是指处于运行状态并且网络功能正常的主机  

如果要判断一个主机是不是活跃主机，往往采用发送数据包的方式只要对方做出回应，我们便可以判断该主机是活跃主机

那么问题来了，我们该向目标主机发送什么数据包，为什么对方受到数据包之后一定要回应呢？

### 网络协议

网络协议往往约定了一台计算机收到数据包后应当如何处理，具体参考Requests For Comments(RFC)文档，这里不深入讨论

##### 下面介绍sn参数

Nmap扫描时默认会对目标的端口进行扫描，但是我们仅仅想知道目标是不是活跃主机，并不需要这些信息，如果详细
扫描时反而会浪费大量的时间，因材使用sn来指定不对目标的端口和其他信息进行扫描

### 基于ARP协议的主机发现技术

优点:准确度高，处于同一网段的所有设备都不能防御这种技术

缺点:不能对处于不同网段的主机进行扫描

`Nmap -PR [目标]`

例如:

`Nmap -sn -PR 127.0.0.1`

扫描结果:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-07 21:33 PDT
Nmap scan report for 114.214.241.161
Host is up.
Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

技术原理:

> 这里有一部分丢失了，之后补全

### 基于TCP协议的主机发现技术

> TCP(Transmission Control Protocol,传输控制协议)是一个位于传输层的协议,它是一种面向连接，可靠的，基于字节
> 流的传输层通信协议。TCP的特点是使用三次握手协议建立可靠连接。

三次握手的过程如下:

1. 首先，客户端发送SYN(SEQ=x)报文给服务器端，进入SYN_SEND状态

2. 其次，服务器端收到SYN报文，回应一个SYN(SEQ=y) ACK(ACK=x+1)报文， 进入SYN_RECV状态

3. 最后，客户端收到服务端的SYN报文，回应一个ACK(ACK=y+1)报文，进入Established状态   
   
此时三次握手完成，TCP客户端和服务端成功建立连接，可以开始传输数据

再理解一下端口的概念，这个概念在ARP和ICMP协议中是没有的。端口可以认为是设备与外界通信的出口。端口又可以分为
虚拟端口和物理端口，这里是用的就是虚拟端口，指的是计算机内部或交换机路由的器内的端口，不可见
> 通常来说，如果检测到一台主机的某个端口开放，那么就说明这个主机是活跃主机

##### TCP SYN扫描

Nmap使用-PS参数向目标主机发送一个SYN标志的数据包，这个数据包内容部分为空。通常默认端口是80，也可以使用参数来改变
目标端口，例如将目标端口改为22，23，113，35000等，当指定多个端口时，Nmap将会并发的对这些端口进行测试

如果目标端口是开放的，按照协议规定，目标主机应当回复一个SYN/ACK数据包，表示同意建立连接，如果目标端口是关闭的，目标主机
就会拒绝这次连接，向Nmap所在主机发送一个RST数据包

但是，在主机发现阶段，我们并不在意目标主机的目标端口是否开放，只在乎主机是否活跃，因此，只要受到数据包，无论是SYN/ACK还是RST
数据包，都说明主机是活跃的。如果没有受到任何数据包，这意味主机不在线

`Nmap -PS [端口1,端口2,…] [目标]`

例如:

`Nmap -sn -PS 49.235.104.114`

扫描结果:

```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-08 23:27 PDT
Nmap scan report for 49.235.104.114
Host is up (0.18s latency).
Nmap done: 1 IP address (1 host up) scanned in 7.96 seconds
```

本次扫描共产生三个数据包
```
发送数据包
Frame 910: 58 bytes on wire (464 bits), 58 bytes captured (464 bits) on interface 0
Ethernet II, Src: IntelCor_41:95:6a (88:78:73:41:95:6a), Dst: Hangzhou_91:72:e2 (5c:dd:70:91:72:e2)
Internet Protocol Version 4, Src: 114.214.244.167, Dst: 49.235.104.114
Transmission Control Protocol, Src Port: 60692, Dst Port: 80, Seq: 0, Len: 0
    Source Port: 60692
    Destination Port: 80
    [Stream index: 6]
    [TCP Segment Len: 0]
    Sequence number: 0    (relative sequence number)
    [Next sequence number: 0    (relative sequence number)]
    Acknowledgment number: 0
    0110 .... = Header Length: 24 bytes (6)
    Flags: 0x002 (SYN)
    Window size value: 1024
    [Calculated window size: 1024]
    Checksum: 0x2f42 [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    Options: (4 bytes), Maximum segment size
    [Timestamps]
```
>Destination Port表示目标端口是80 Flag的值为0x002(SYN)表示这是一个SYN数据包
```
接受数据包
Frame 917: 60 bytes on wire (480 bits), 60 bytes captured (480 bits) on interface 0
Ethernet II, Src: Hangzhou_91:72:e2 (5c:dd:70:91:72:e2), Dst: IntelCor_41:95:6a (88:78:73:41:95:6a)
Internet Protocol Version 4, Src: 49.235.104.114, Dst: 114.214.244.167
Transmission Control Protocol, Src Port: 80, Dst Port: 60692, Seq: 0, Ack: 1, Len: 0
    Source Port: 80
    Destination Port: 60692
    [Stream index: 6]
    [TCP Segment Len: 0]
    Sequence number: 0    (relative sequence number)
    [Next sequence number: 0    (relative sequence number)]
    Acknowledgment number: 1    (relative ack number)
    0110 .... = Header Length: 24 bytes (6)
    Flags: 0x012 (SYN, ACK)
    Window size value: 29200
    [Calculated window size: 29200]
    Checksum: 0x9304 [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    Options: (4 bytes), Maximum segment size
    [SEQ/ACK analysis]
    [Timestamps]
```
>按照规定，目标主机应当发送一个SYN/ACK数据包作为回复
```
回复数据包
Frame 918: 54 bytes on wire (432 bits), 54 bytes captured (432 bits) on interface 0
Ethernet II, Src: IntelCor_41:95:6a (88:78:73:41:95:6a), Dst: Hangzhou_91:72:e2 (5c:dd:70:91:72:e2)
Internet Protocol Version 4, Src: 114.214.244.167, Dst: 49.235.104.114
Transmission Control Protocol, Src Port: 60692, Dst Port: 80, Seq: 1, Len: 0
    Source Port: 60692
    Destination Port: 80
    [Stream index: 6]
    [TCP Segment Len: 0]
    Sequence number: 1    (relative sequence number)
    [Next sequence number: 1    (relative sequence number)]
    Acknowledgment number: 0
    0101 .... = Header Length: 20 bytes (5)
    Flags: 0x004 (RST)
    Window size value: 0
    [Calculated window size: 0]
    [Window size scaling factor: -2 (no window scaling used)]
    Checksum: 0x4afb [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    [Timestamps]
```
>由于Nmap并不会真的与主机建立连接，只是为了判断目标主机是否是活跃主机，因此发送一个RST数据包来结束本次连接

倘若我们发送一个数据包到目标10000端口

例如:

`Nmap -sn -PS10000 49.235.104.114`

>注意，PS与端口号之间不能有空格

扫描结果:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-09 21:36 PDT
Nmap scan report for 49.235.104.114
Host is up (0.070s latency).
Nmap done: 1 IP address (1 host up) scanned in 7.20 seconds
```
```
发送数据包
Frame 33: 58 bytes on wire (464 bits), 58 bytes captured (464 bits) on interface 0
Ethernet II, Src: IntelCor_41:95:6a (88:78:73:41:95:6a), Dst: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc)
Internet Protocol Version 4, Src: 192.168.43.130, Dst: 49.235.104.114
Transmission Control Protocol, Src Port: 51754, Dst Port: 10000, Seq: 0, Len: 0
    Source Port: 51754
    Destination Port: 10000
    [Stream index: 2]
    [TCP Segment Len: 0]
    Sequence number: 0    (relative sequence number)
    [Next sequence number: 0    (relative sequence number)]
    Acknowledgment number: 0
    0110 .... = Header Length: 24 bytes (6)
    Flags: 0x002 (SYN)
    Window size value: 1024
    [Calculated window size: 1024]
    Checksum: 0x4714 [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    Options: (4 bytes), Maximum segment size
    [Timestamps]
```
```
接收数据包
Frame 34: 54 bytes on wire (432 bits), 54 bytes captured (432 bits) on interface 0
Ethernet II, Src: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc), Dst: IntelCor_41:95:6a (88:78:73:41:95:6a)
Internet Protocol Version 4, Src: 49.235.104.114, Dst: 192.168.43.130
Transmission Control Protocol, Src Port: 10000, Dst Port: 51754, Seq: 1, Ack: 1, Len: 0
    Source Port: 10000
    Destination Port: 51754
    [Stream index: 2]
    [TCP Segment Len: 0]
    Sequence number: 1    (relative sequence number)
    [Next sequence number: 1    (relative sequence number)]
    Acknowledgment number: 1    (relative ack number)
    0101 .... = Header Length: 20 bytes (5)
    Flags: 0x014 (RST, ACK)
    Window size value: 0
    [Calculated window size: 0]
    [Window size scaling factor: -2 (no window scaling used)]
    Checksum: 0x62bd [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    [SEQ/ACK analysis]
    [Timestamps]
```
> 10000端口是关闭的，但是我们收到了一个RST数据包，因此我们仍然可以判断目标主机是活跃的

TCP扫描是Nmap扫描技术中最为强大的技术之一，很多服务器的防御机制会屏蔽ICMP echo的数据包，但是
任何服务器都会响应针对其服务的SYN数据包，虽然也有可能拒绝ACK数据包。

但是值得注意的是，很多服务器的安全机制可能会屏蔽掉它提供服务以外的端口，比如web服务器会把80
端口以外的所有端口全部屏蔽掉，因此当你发一个SYN包去往它的22端口时，可能会石沉大海，没有任何响应

而我们有很难对大量主机的所有端口进行扫描，因此端口的选择十分重要，下表列出了常用的14个端口
端口号|提供的服务|端口号|提供的服务
------|----------|------|----------
80    |http      |53    |domain
25    |smtp      |554   |rtsp
22    |ssh       |3389  |ms-term-server
443   |https     |1723  |pptp
21    |ftp       |389   |ldap
113   |auth      |636   |ldapssl
23    |telnet    |256   |fw1-securemote

在利用端口扫描时，也可以使用这些端口的组合，比如“-PS22，80”就是一个不错的组合，但“-PS80，443”意义就不大
，因为如果目标是一个web服务器，那么他基本上就会提供这两个服务，如果不是，那么一个都不会有

##### TCP ACK扫描

如果Nmap向目标主机发送一个TCP/ACK包，那么目标主机显然不清楚这是怎么一回事，因此不可能成功建立连接，因此，
通常会回复一个RST标志位的数据包

```Nmap -PA [端口号1,端口号2,…] [目标]```

例如:

```Nmap -sn -PA 49.235.104.114```

扫描结果:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-09 21:56 PDT
Nmap scan report for 49.235.104.114
Host is up (0.074s latency).
Nmap done: 1 IP address (1 host up) scanned in 12.17 seconds
```

```
发送的数据包：
Frame 932: 54 bytes on wire (432 bits), 54 bytes captured (432 bits) on interface 0
Ethernet II, Src: IntelCor_41:95:6a (88:78:73:41:95:6a), Dst: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc)
Internet Protocol Version 4, Src: 192.168.43.130, Dst: 49.235.104.114
Transmission Control Protocol, Src Port: 45577, Dst Port: 80, Seq: 1, Ack: 1, Len: 0
    Source Port: 45577
    Destination Port: 80
    [Stream index: 18]
    [TCP Segment Len: 0]
    Sequence number: 1    (relative sequence number)
    [Next sequence number: 1    (relative sequence number)]
    Acknowledgment number: 1    (relative ack number)
    0101 .... = Header Length: 20 bytes (5)
    Flags: 0x010 (ACK)
    Window size value: 1024
    [Calculated window size: 1024]
    [Window size scaling factor: -1 (unknown)]
    Checksum: 0xeac9 [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    [Timestamps]
```
```
收到的数据包：
Frame 933: 54 bytes on wire (432 bits), 54 bytes captured (432 bits) on interface 0
Ethernet II, Src: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc), Dst: IntelCor_41:95:6a (88:78:73:41:95:6a)
Internet Protocol Version 4, Src: 49.235.104.114, Dst: 192.168.43.130
Transmission Control Protocol, Src Port: 80, Dst Port: 45577, Seq: 1, Len: 0
    Source Port: 80
    Destination Port: 45577
    [Stream index: 18]
    [TCP Segment Len: 0]
    Sequence number: 1    (relative sequence number)
    [Next sequence number: 1    (relative sequence number)]
    Acknowledgment number: 0
    0101 .... = Header Length: 20 bytes (5)
    Flags: 0x004 (RST)
    Window size value: 0
    [Calculated window size: 0]
    [Window size scaling factor: -1 (unknown)]
    Checksum: 0xeed5 [unverified]
    [Checksum Status: Unverified]
    Urgent pointer: 0
    [Timestamps]
```
> 注意：实际上这种扫描很少成功，因为目标主机会过滤掉这种莫名其妙的TCP/ACK包

如果没有收到回包，一种情况是因为这个包被过滤了，另一种情况是目标主机非活跃主机，但是Nmap会按照第二种
情况进行判断

### 基于UDP协议的主机发现技术

UDP也是一个位于传输层的协议。它完成的工作于TCP是相同的，但是由于UDP不是面向连接的，对UDP端口的探测也就不可能像
TCP探测那样依赖于连接建立过程，这也使得UDP扫描可靠性不高。

因此，虽然UDP协议较之TCP协议简单，但对UDP的扫描确是相当困难的。

当一个UDP端口收到一个UDP数据包时，如果它是关闭的，就会给源端发送一个ICMP端口不可达数据包; 如果它是开放的，就会
忽略这个数据包，也即是将他丢弃而不返回任何数据包

这样做的优点是可以完成对UDP端口的探测，而缺点是扫描结果的可靠性较低。因为当发出一个UDP数据包而没有收到任何应答时，
可能是因为这个UDP端口是开放的，也有可能是因为这个数据包在传输过程丢失了。

另外，扫描的速度也很慢，原因在于RFC1812中对于ICMP错误报文的生成速度作出了限制，例如：Linux就将ICMP报文的生成速度改为每
4s产生80个，当超出这个限制时，还要暂停1/4s。

##### UDP 扫描

`Nmap -PU [目标]`

例如：

`Nmap -sn -PU 49.235.104.114`

扫描结果
```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-09-09 22:12 PDT
Nmap scan report for 49.235.104.114
Host is up (0.063s latency).
Nmap done: 1 IP address (1 host up) scanned in 13.16 seconds
```
```
发送数据包：
Frame 1675: 42 bytes on wire (336 bits), 42 bytes captured (336 bits) on interface 0
Ethernet II, Src: IntelCor_41:95:6a (88:78:73:41:95:6a), Dst: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc)
Internet Protocol Version 4, Src: 192.168.43.130, Dst: 49.235.104.114
User Datagram Protocol, Src Port: 59394, Dst Port: 40125
    Source Port: 59394
    Destination Port: 40125
    Length: 8
    Checksum: 0xf495 [unverified]
    [Checksum Status: Unverified]
    [Stream index: 325]
```
> 可以看出，使用的协议是UDP，目标端口是40125
```
收到数据包：
Frame 1676: 70 bytes on wire (560 bits), 70 bytes captured (560 bits) on interface 0
Ethernet II, Src: VivoMobi_97:c1:bc (08:23:b2:97:c1:bc), Dst: IntelCor_41:95:6a (88:78:73:41:95:6a)
Internet Protocol Version 4, Src: 49.235.104.114, Dst: 192.168.43.130
Internet Control Message Protocol
    Type: 3 (Destination unreachable)
    Code: 3 (Port unreachable)
    Checksum: 0x955a [correct]
    [Checksum Status: Good]
    Unused: 00000000
    Internet Protocol Version 4, Src: 192.168.43.130, Dst: 49.235.104.114
    User Datagram Protocol, Src Port: 59394, Dst Port: 40125
        Source Port: 59394
        Destination Port: 40125
        Length: 8
        Checksum: 0xe2d9 [unverified]
        [Checksum Status: Unverified]
        [Stream index: 325]
```
> 由于目标端口是关闭的，因此会发送一个ICMP端口不可达，Type值为3

因此，针对UDP扫描，端口选择也很重要，但是与TCP不同，UDP需要扫描的是目标主机关闭的端口。在扫描过程中，
需要避开那些常用的UDP协议端口，例如DNS(端口53),SNMP(端口161)。因此，最合适的做法就是选择一个值比较大的端口，
例如35462。
