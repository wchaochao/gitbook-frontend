# 物理层

标签（空格分隔）： 前端知识体系

---

## 调制

将数据转换为传输媒介信号

### 基带调制

* 不归零制：正电平代表1，负电平代表0
* 归零制：正脉冲代表1，负脉冲代表0
* 曼侧斯特编码：周期中心向下跳变代表1，向上跳变代表0
* 差分曼侧斯特编码：开始边界无跳变代表1，有跳变代表0

![基带调制](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/baseband-modulation.png)

### 载波调制

* 调幅：载波的振幅随基带数字信号而变化，如0对应无载波，1对应有载波
* 调频：载波的频率随基带数字信号而变化，如0对应频率f0，1对应频率f1
* 调相：载波的初始相位随基带数字信号而变化，如0对应相位0度，1对应相位180度

![载波调制](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/carrier-modumlation.png)

## 传输媒介

### 导引型

* 同轴电缆：由内导体铜芯、绝缘层、外导体屏蔽层以及绝缘保护套组成
* 双绞线：两根相互绝缘的铜导线绞合在一起
 * 屏蔽双绞线：在双绞线外面加上一层用金属丝编织成的屏蔽层
* 光纤：由纤芯和包层构成
 * 多模光纤：存在多条不同角度入射的光线在一条光纤中传输，适合近距离传输
 * 单模光纤：光纤的直径为光的波长，光线一直向前传播而不会发生多次反射，适合远距离传输

### 非导引型

* 短波通信：3MHz ~ 30MHz，靠电离层的反射通信
* 无线电微波通信：300MHz ~ 300GHz，在空间中直线传播，如地面微波接力通信、卫星通信

## 信道

沿某一方向传送信号的媒介

### 分类

* 单工通信：单向通信，只能往一个方向的发送数据，如无线电广播、电视广播
* 半双工：双向交替通信，通信的双方都能发送数据，但不能同时发送
* 全双工：双向同时通信，通信的双方可以同时发送和接收数据

### 复用

* 频分复用：所有用户在同样的时间占用不同的带宽资源
 * 波分复用：光的频分复用
* 时分复用：所有用户在不同的时间占用同样的带宽资源
 * 统计时分复用：用户没有数据发送时跳过该用户
* 码分复用：将一个码元划分为码片系列

### 极限

码元在信道上的传输速率是有上限，超过此上限时会出现严重的码间串扰问题，接收端无法识别码元

* 带宽：信道的频率范围，带宽越大，信道的极限传输速率越大
* 信噪比：信号的平均功率与噪声的平均功率之比，信噪比越大，信道的极限传输速率越大
* 每一个码元携带的比特越多，信道的极限传输速率越大

## 宽带接入

用户接入运营商网络的技术

### ADSL

Asymmetric Digital Subscriber Line，非对称数字用户线，对现有的模拟电话用户线进行改造以承载数字业务

* 0 ~ 4kHz低频频谱：传输电话信号
* 剩下的高频频谱：采用频分复用的方式划分为多个子信道，传输载波调制的数字信号
 * 25个上行信道
 * 249个下行信道

![ADSL接入1](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ADSL-1.png)

![ADSL接入2](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/ADSL-2.png)

### FFTH

光纤到户

* 光线路终端OLT（Optical Line Terminal）接收光纤干线中的传输的数据发往1:N光分路器
* 光分路器向所有用户端的光网络单元ONU（Optical Network）广播
* 光网络单元根据特有标志只接收发送给自己的数据，转换为电信号发往用户家中

![FFTH接入](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/fiber-split.png)

![FFTH接入2](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/fiber-split-2.png)

### HFC

Hybrid Fiber Coax，光纤同轴混合网，在有线电视网的基础上开发的宽度接入网

* 数字电视信号通过光纤干线传输到光纤节点转换为电信号
* 电信号通过同轴电缆传送到每个用户家庭，通过机顶盒内的电缆调整解调器解调为电视信号显示
