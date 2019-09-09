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

[TCP/IP模型]("data:image/png;base64,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

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

