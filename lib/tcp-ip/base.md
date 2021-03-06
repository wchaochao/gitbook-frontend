# 计算机网络

标签（空格分隔）： 前端知识体系

---

存储和交换信息

## 组成

![计算机网络](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/LAN.png)

### 局域网

* 主机：产生数据、接收数据
* 网卡：将数据转换为能在传输媒介中传输的信号
* 传输媒介：通过信道传输信号
* 集线器：接收信号并广播给其他设备
* 交换机：根据MAC地址在数据链路层内转发信号
* 路由器：根据IP地址在网络层转发信号

![局域网](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/tcp-ip-internet.png)

### 接入网

接入运营商网络

* 互联网接入服务器：将数据转换为接入信号
* 宽带接入服务器：将接入信号转换为数据，进行登录认证、配置下发或转发给隧道路由器
* 隧道路由器：将数据转发给运营商网络接入点

![接入网](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/POP.png)

### 骨干网

* POP：Point of Presense，运营商网络接入点
* NOC：Network Operation Center，网络运行中心，连接运营商的多个POP
* IX：Internet Exchange，互联网交换中心，连接多个运营商

![骨干网](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/internet-construct.png)

## 信号交换

按照某种方式动态分配传输线路的资源

### 电路交换

用户始终占用端到端的通信资源

* 建立连接：占用通信资源
* 通话：一直占用通信资源
* 释放连接：归还通信资源

### 分组交换

存储转发，逐段占用通信链路

* 主机将报文划分为多个分组发送
* 路由器接收到分组后，先存储下来
* 路由器根据路由表将分组转发到下一个节点

## 性能指标

### 速率

* 发送速率：发送端发送数据的速率
* 带宽：信道所能传输的最高速率
* 吞吐量：单位时间内通过某个网络的实际数据量

### 时延

* 发送时延：发送端发送数据的时间
* 传播时延：数据在信道中传播的时间
* 排队时延：分组在路由器中排队的时间
* 处理时延：接收端接收数据的时间
* 往返时间RTT：Round-Trip Time，数据往返一次的时间

### 利用率

* 信道利用率：信道有百分之几的时间是被利用的
* 网络利用率：全网络的信道利用率的加权平均值，利用率越高，时延越大

## 分层协议

![分层协议](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/internet-model.png)

### 物理层

连接设备，形成通路，传输信号

* 发送端将数据转换成传输媒介信号
* 信号通过传输媒介的信道传播
* 接收端将传输媒介信号转换回数据

### 数据链路层

在互联设备上传输数据包

* 以太网协议：通过广播传输数据包
* PPP协议：通过点对点传输数据包

### 网络层

根据路由表和IP地址进行路由选择，建立主机到主机的通信

* ARP协议：根据IP地址解析出MAC地址
* IP协议：根据IP地址进行寻址
* ICMP协议：告知IP数据包传输过程中产生的错误以及各种控制信息

### 传输层

根据端口号建立进程到进程的通信

* UDP协议：提供无连接的、尽最大努力的数据传输服务
* TCP协议：提供面向连接的、可靠的数据传输服务

### 应用层

建立程序到程序的通信

* DNS协议：DNS查询
* DHCP协议：动态分配IP
* HTTP协议：Web访问
* HTTPS协议：安全的Web访问
* Telnet协议：远程登录
* SSH协议：安全的远程登录
* FTP协议：上传下载文件
* SMTP协议：收发邮件
* SNMP协议：网络管理

## OSI七层模型

* 应用层：规定应用程序通信的相关细节
* 表示层：应用处理信息与网络标准数据格式的转换
* 会话层：通信管理，负责何时建立和断开通信连接
* 传输层：管理两个节点之间的实际连接和数据传输
* 网络层：负责寻址和路由选择
* 数据链路层：互联设备之间传送和识别数据帧
* 物理层：比特流与电子信号之间的转换

### 数据流

发送端从应用层往下走，接收端从物理层往上走

* 发送端在层与层传输数据时，每经过一层时加上该层的首部消息
* 接收端在层与层传输数据时，每经过一层时删去该层的首部消息

![数据流](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/tcp-package.png)
