# 数据链路层

标签（空格分隔）： 前端知识体系

---

## 包

网络传输的最小单位

* 首部：包定界符，标志包的开始
* 载荷：承载上层消息，如以太网消息、PPP消息
* 尾部：帧检验序列FCS，用于检测比特差错

![包](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/nic-package.png)

## 网卡

网络适配器、接口，发送和接收包的计算机硬件

![网卡](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/nic.png)

### 分类

* 以太网卡
* 无线网卡
* 环回网卡
* 蓝牙网卡
* 虚拟网卡

### MAC地址

网卡的硬件地址，用6字节表示

* 前24位：厂商识别码
* 后24位：厂商内识别码

![MAC地址](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/mac-address.png)

### ipconfig命令

windows系统的网卡配置

| 命令 | 说明 |
| --- | --- |
| `ipconfig` | 查看所有网卡的基本信息 |
| `ipconfig /all` | 查看所有网卡的全部信息 |
| `ipconfig /release` | 释放动态分配的IP地址 |
| `ipconfig /renew` | 更新动态分配的IP地址 |
| `ipconfig /displaydns` | 显示DNS缓存 |
| `ipconfig /flushdns` | 清除DNS缓存 |

### ifconfig命令

Linux系统的网卡配置

| 命令 | 说明 |
| --- | --- |
| `ifconfig` | 查看所有网卡的基本信息 |
| `ifconfig -a` | 查看所有网卡的全部信息 |
| `ifconfig <interface> hw ether <MAC>` | 配置网卡的MAC地址，重启后失效 |
| `ifconfig <interface> <ip> [netmask <netmask> [broadcast <broadcast>]]` | 配置网卡的IP地址、子网掩码、广播地址，重启后失效 |
| `ifconfig <interface> -arp` | 关闭网卡的arp协议 |
| `ifconfig <interface> arp` | 开启网卡的arp协议 |

## 以太网协议

使用一对多的广播通信

![以太网](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ethernet.png)

### 格式

首部

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Destination | 6byte | 目的MAC地址 |
| Source | 6byte | 源MAC地址 |
| Type | 2byte | 上层协议类型，如0x0806 （ARP协议），0x0800（IP协议）|

载荷：46到1500字节

### 总线型

采用CSMA/CD（Carrier Sense Multiple Access with Collsion Detection）协议

* 准备发送：网卡从网络层获得一个分组，封装成以太网包，放入缓存中
* 检测信道：检测到信道空闲，且在96比特时间内保持空闲（保证包最小间隔），发送包
* 碰撞检测：发送包的过程中继续检测信道
 * 在争用期内未检测到碰撞，帧发送成功
 * 在争用期内检测到碰撞，立即停止发送数据并发送人为干扰信号，接着执行指数退避算法，等待随机个争用期时间后重新发送包，重传16次仍未成功时向上报错

![CSMA/CD1](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-base/ethernet-CSMA.png)

![CSMA/CD2](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-base/ethernet-CD.png)

### 集线器型

接收到信号时对连接到集线器上的所有设备进行广播

* 每个主机通过一根电缆内的两对双绞线（分别用于发送和接收）与集线器的接口相连，形成星形拓扑
* 使用集线器的以太网逻辑上仍是个总线网，仍使用CSMA/CD协议

![集线器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/hub.png)

### 交换机型

根据CAM表（MAC地址-端口映射表）将包转发给目的地

* 源主机将数据发送给交换机
* 交换机将源MAC地址和接入交换机的端口记录到CAM表中
* 交换机在CAM表中查找目标MAC地址的端口
 * 未找到，向其他主机广播
 * 已找到，通过该端口转发给目标主机

![交换机](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/switch.png)

## PPP协议

Point-to-Point Protocol，点对点协议，拨号上网使用的协议

### 格式

首部

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Type | 2byte | 上层协议类型，0x0021（IP协议）、0xC021（LCP协议）、0x8021（NCP协议） |

载荷

* LCP：Link Control Protocol，链路控制协议，用于建立、配置、测试连接
* NCP：Network Control Protocol，网络控制协议，支持网络层协议

### 交互

* 建立LCP链接
* 协商LCP配置
* 鉴权
 * PAP：Password Authentication Protocol，使用用户名和密码鉴权
 * CHAP：Challenge-Handshake Authentication Protocol，使用随机数和密码计算成的hash值鉴权
* 协商NCP配置
* 传输网络层数据

![PPP交互](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/PPP-protocol.png)

### 数据流

PPP消息装入HDLC包中进行传输

![PPP消息流](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/PPP-auth.png)

## PPPoE协议

Point-to-Point Protocol over Ethernet，以太网的点对点协议，宽带上网使用的协议

### 格式

首部

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Version | 4bit | PPPoE的版本号 |
| Type | 4bit | 类型，未使用 |
| Encode | 1byte | 编码 |
| SessionID | 2byte | 会话ID |
| Length | 2byte | 载荷长度 |

载荷：承载PPP消息

### ADSL接入

![ADSL接入](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ADSL-PPP.png)

### FTTH接入

![FTTH接入](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/FTTH-PPP.png)

## PPPOA协议

Point-to-Point Protocol over ATM，ATM的点对点协议，ADSL接入网可使用的方式

![PPPOA](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/PPPoA.png)
