# 网络层

标签（空格分隔）： 前端知识体系

---

主机与主机间的通信

## IP地址

给互联网主机或路由器的每一个网络接口分配的唯一的32位标识符

### 分类IP

IP地址 ::= {<网络号>，<主机号>}，采用点分十进制记法

* 网络号：主机或路由器所在的网络
* 主机号：网络中的某个主机或路由器
 * 主机号全为0：表示当前网络
 * 主机号全为1：表示当前网络上的所有主机

| 分类 | 网络号 | 主机号 | 地址个数 | 特殊网络号 |
| --- | --- | --- | --- | --- |
| A类 | 一个字节，第一位为0 | 三个字节 | 2^31个，占50% | 网络号全为0：保留地址，表示本网络<br/>网络号全为1：环回地址，表示本主机 |
| B类 | 两个字节，前两位为10 | 两个字节 | 2^30个，占25% | 网络号128.0不指派 |
| C类 | 三个字节，前三位为110 | 一个字节 | 2^29个，占12.5% | 网络号192.0.0不指派 |


* D类地址：前4位为1110，用于多播
* E类地址：前4位为1111，保留为以后用

### 特殊IP

| 网络号 | 主机号 | 表示 |
| --- | --- | --- |
| 0 | 0 | 本网络的本主机 |
| 0 | host-id | 本网络的某主机 |
| 127 | 非全0或全1 | 本地软件环回测试 |
| net-id | 全1 | 对net-id上的所有主机进行广播 |
| 全1 | 全1 | 在本网络上进行广播 |

### 子网划分

### 超网构造

## 路由器

专用计算机，用来在互联网中进行路由选择

## IP协议

Internet Protocol，网际协议，使互连起来的多种异构网络进行通信

### 格式

首部，固定部分20字节 + 可变部分

| 字段 | 占用空间 | 说明 |
| --- | --- | --- |
| 版本 | 4bit | IP协议的版本号，IPv4或IPv6 |
| 首部长度 | 4bit | 首部的长度，单位为4字节，范围为20字节到60字节，不足时填充 |
| 区分服务 | 1byte | 使用区分服务时才起作用 |
| 总长度 | 2byte | 首部与数据之和，单位为字节，最大长度为65535字节，超过MTU时要分片 |
| 标识 | 2byte | IP数据报的标志，用于分片后组装 |
| 标志 | 3bit | 第一位MF：后面是否还有分片<br/>第二位DF：是否不允许分片<br/>第三位保留 |
| 片偏移 | 13bit | 分片在原分组的相对位置，单位为8字节 |
| 字段 | 说明 | 占用空间 |

### 数据传输

* 主机查找自己的路由表
 * 目的主机在本网络上，直接交付
 * 目的主机不在本网络上，发送给路由器
* 路由器查找自己的路由表，不断通过路由器传递，直到目的主机

## ARP协议

Address Resolution Protocol，地址解析协议

### ARP高速缓存

本局域网上主机的IP地址和MAC地址的映射表

* 目的IP地址在APR映射表中，返回对应的MAC地址
* 目的IP地址不在APR映射表中，向本局域网的所有主机广播APR请求
 * 主机接收到APR请求是，对比IP地址，一致时返回APR响应，并将源主机的IP地址、MAC地址写入自己的APR映射表
 * 源主机接收到APR响应后，将目的主机的IP地址、MAC地址写入自己的APR映射表中
* APR映射表中的地址项目都设置了生存时间，超过生存时间会被删除

## ICMP协议

Internet Control Message Protocol，网际控制报文协议

## IGMP协议

Internet Group Managament Protocol，网际组管理协议