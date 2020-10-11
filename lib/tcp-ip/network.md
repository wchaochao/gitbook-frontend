# 网络层

标签（空格分隔）： 前端知识体系

---

## IP地址

分配给网卡的软件地址

![IP地址](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ip-address.png)

### 版本

* IPv4：32位，主流版本
* IPv6：128位，下一代IP地址

### 组成

IP地址 ::= {<网络号>，<主机号>}

* 网络号：主机或路由器所在的网络
* 主机号：网络中的某个主机或路由器
 * 主机号全为0：表示当前网络
 * 主机号全为1：表示当前网络上的所有主机，当前网络的广播地址

### 子网掩码

由32位二进制数组成，左边为1，右边为0，用于区分网络地址和主机地址

* 1对应网络号
* 0对应主机号

![子网掩码](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/subnet-mask.png)

### 分类

| 网络类别 | IP地址范围 | 子网掩码 | 最大网络数 | 最大主机数 |
| --- | --- | --- | --- | --- | --- |
| A类 | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 | 126 | 166777214 |
| B类 | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 | 16384 | 65534 |
| C类 | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 | 2097152 | 254 |
| D类 | 224.0.0.0 - 239.255.255.255 | 无 | 无 | 无 |
| E类 | 240.0.0.0 - 255.255.255.255 | 无 | 无 | 无 |

![IP地址分类](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ip-address-category.png)

### 特殊IP

* 0.0.0.0: 所有不清楚的主机和目的网络的集合
* 255.255.255.255: 受限广播地址，只能在子网内广播
* 127.0.0.1: 本机地址，用于本机环回测试

### 私网地址

访问互联网时需要做NAT或PAT网络地址转换

* A类：10.0.0.0 - 10.255.255.255
* B类：172.16.0.0 - 172.31.255.255
* C类：192.168.0.0 - 192.168.255.255

![私网访问](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/address-convert.png)

### 子网划分

对主机号进行再划分，将一部分划入网络号

![子网划分](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/subnet-divide.png)

### 超网合并

## 路由表

### 组成

* 目标地址：匹配目的IP地址
* 子网掩码：匹配目的IP地址时需要比对的比特数量
* 网关：转发给哪个路由器，没有时直接转发给目的地
* 接口：从哪个网卡转发
* 跃点数：距离目的IP地址的跳数

![路由表](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/router-table.png)

### 匹配

* 多个路由表记录匹配时，选择匹配比特数多的和跃点数少的
* 没有路由表记录匹配时选择默认路由，即目标地址为0.0.0.0的记录
* 没有路由表记录匹配且没有默认路由时，丢弃这个包并发送ICMP消息告知发送方

### route命令

windows系统的路由表操作

| 命令 | 说明 |
| --- | --- |
| `route print` | 显示路由表的完整信息 |
| `route print -4` | 显示IPV4的路由表信息 |
| `route add <destination> mask <netmask> <gateway>` | 添加路由 |
| `route delete <destination>` | 删除路由 |
| `route change <destination> mask <netmask> <gateway>` | 修改网关 |

Linux系统的路由表操作

| 命令 | 说明 |
| --- | --- |
| `route` | 显示路由表的完整信息 |
| `route add <destination> netmask <netmask> gw <gateway>` | 添加路由 |
| `route del <destination>` | 删除路由 |
| `route add default gw <gateway>` | 添加默认路由 |
| `route del default gw <gateway>` | 删除默认路由 |

## ARP协议

Address Resolution Protocol，基于以太网协议的地址解析协议，根据IP地址查询MAC地址

### 格式

首部

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Hardware type | 2byte | 硬件类型，标识链路层协议，如0x0001（以太网协议） |
| Protocol Type | 2byte | 协议类型，标识网络层协议，如0x0800（IP协议） |
| Hardware size | 1byte | 硬件地址大小，单位字节，标识MAC地址长度 |
| Protocol size | 1byte | 协议地址大小，单位字节，标识IP地址长度 |
| Opcode | 2byte | 操作代码，标识ARP数据包类型，如0x0001（ARP请求），0x0002（ARP响应） |

载荷

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Sender MAC address | 6byte | 发送者MAC地址 |
| Sender IP address | 4byte | 发送者IP地址 |
| Target MAC Address | 6byte | 目标MAC地址，不知道时为00:00:00:00:00:00 |
| Target IP Address | 4byte | 目标IP地址 |

![ARP数据包](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/arp-package.png)

### 查询

* 目的IP地址在APR表（IP-MAC映射表）中，返回对应的MAC地址
* 目的IP地址不在APR映射表中，向本局域网的所有主机广播APR请求
 * 主机接收到APR请求时，对比IP地址，一致时返回APR响应，并将源主机的IP地址、MAC地址写入自己的APR表中
 * 源主机接收到APR响应后，将目的主机的IP地址、MAC地址写入自己的APR表中
* APR映射表中的地址项目都设置了TTL生存时间，超过生存时间会被删除，更新时后面的会覆盖前面的

![ARP协议](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/arp-broadcast.png)

### ARP命令

### ARP攻击

## IP协议

Internet Protocol，网际协议，根据IP地址进行路由

### 格式

头部，固定部分20字节 + 可变部分

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Version | 4bit | 版本号，IPv4或IPv6 |
| Length | 4bit | 头部长度，单位为4字节，范围为20字节到60字节，不足时填充 |
| Service Type | 1byte | 服务类型 |
| Total Length | 2byte | IP消息总长度，单位为字节，最大长度为65535字节，超过MTU时要分片 |
| Identity | 2byte | IP数据报的标志，用于分片后组装 |
| Flag | 3bit | 分片标志位<br/>第一位MF：后面是否还有分片<br/>第二位DF：是否不允许分片<br/>第三位保留 |
| offset | 13bit | 片偏移，分片在原分组的相对位置，单位为8字节 |
| TTL | 1byte | 包的生存时间，每经过一个路由器转发就减1 |
| Protocol Type | 1byte | 上层协议类型 |
| FCS | 2byte | 首部校验和 |
| Source IP Address | 4byte | 源IP地址 |
| Destination IP Address | 4byte | 目的IP地址 |
| Options | 可选 | 可选 |

![IP头部](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ip-header.png)

载荷：承载ICMP、UDP、TCP等消息

### 数据流

* 主机查找自己的路由表
 * 目的主机在本网络上，直接交付
 * 目的主机不在本网络上，发送给路由器
* 路由器查找自己的路由表，不断通过路由器传递，直到目的主机

## ICMP协议

Internet Control Message Protocol，网际控制报文协议，告知IP数据包传输过程中产生的错误以及各种控制信息

### 格式

头部

| 字段 | 长度 | 含义 |
| --- | --- | --- |
| Type | 1byte | ICMP消息类型 |
| Code | 1byte | Type下的小类型 |
| FCS | 1byte | 校验和 |

载荷

| 消息类型 | 说明 |
| --- | --- |
| Echo | ping命令发送的消息，用于确认接收端是否存在 |
| Echo reply | Echo的响应消息 |
| Destination Unreachable | 目的地无法到达 |
| Redirect | 重定向，直接发送不需路由器转发 |

![ICMP消息](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/icmp-header.png)

## IGMP协议

Internet Group Managament Protocol，网际组管理协议
