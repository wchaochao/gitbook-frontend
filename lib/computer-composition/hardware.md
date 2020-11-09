# 硬件组成

标签（空格分隔）： 前端知识体系

---

数字计算机是由处理器、存储器、输入/输出设备组成的内部互连系统

![计算机组成](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/composition.png)

## 处理器

CPU（中央处理单元），从主存储器中逐条进行取指令、分析指令、执行指令操作

* 控制器：从主存储器中取指令和分析指令
* 算术逻辑单元：通过算术逻辑运算来执行指令
* 寄存器组：存放中间结果和控制信息
 * 程序计数器：指向下一条指令
 * 指令寄存器：存放当前指令
 * 累加器：存放累加结果

### 数据通路

寄存器组提供数据给算术逻辑单元，运算后将结果存入寄存器

![数据通路](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/data-path.png)

### 指令

* 寄存器-主存指令：用于寄存器和主存之间交换数据，将主存当中的字取到寄存器或将寄存器中的数据存入主存
* 寄存器-寄存器指令：对寄存器中的数据进行运算后再将结果存入寄存器

### 指令集

* CISC：Complex Instruction Set Computer，复杂指令计算机，如x86系列
* RISC：Reduced Instruction Set Computer，精简指令计算机，如ARM系列

### 指令执行

* 取指
 * 从主存中取下一条指令到指令寄存器
 * 将程序计数器指向后一条指令
* 译码
 * 判断指令类型和指令需要的操作数
 * 需要时对主存单元进行寻址，取得操作数到寄存器
* 执行
 * 寄存器组提供操作数给算术逻辑单元
 * 算术逻辑单元运算后将结果存入寄存器

### 指令并行

采用指令流水的方式将指令执行分解成多个部分，各部分并行运行

![指令并行](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/assembly-line.png)

双流水线，一次取两条指令中，这两条指令使用的资源不能冲突

![双流水线](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/double-assembly-line.png)

超标量CPU，一条流水线，一个时钟周期内启动多条指令

![超标量](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/superscalar.png)

### 处理器并行

SMID，单指令流多数据流处理器，由一个控制器控制多个处理器，对一组数据中的每一个分别执行相同的操作

![SIMD](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/SIMD.png)

多处理器，由多个CPU和共享内存组成，互相协调完成一个任务

![多处理器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/multiprocessor.png)

多计算机，由多个计算机通过高速网络连接组成，共同完成一个任务

### 设计原则

* 所有指令由硬件直接执行
* 最大限度地并行执行指令
* 指令应容易译码
* 只允许读写主存指令访问主存
* 提供足够的寄存器

## 存储器

存储数据

### 存储结构

访问时间逐渐增长，存储容量逐渐增大、

* 寄存器：存取速度可以满足CPU的要求
* 高速缓存：缓存主存储器中的指令和数据
* 主存储器：存储正在运行的程序
* 固态硬盘和磁盘：存储永久数据
* 磁带和光盘：后备存储

![存储层次](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/storage-layer.png)

### 存储分类

不依赖电源

* ROM：只读存储器，一旦写入数据后就不能修改或删除，无需电源
* 硬盘：可读写，永久存储数据

依赖电源

* RAM：随机存储器，可以以任意顺序访问，一旦掉电就会丢失数据
 * SRAM：静态随机存储器，加电后就可以一直保存数据，如高速缓存
 * DRAM：动态随机存储器，加电后数据只能保持很短的时间，需要不断刷新，如主存储器
 * SDRAM：同步动态随机存储器，同步时钟
 * DDR SDRAM：双倍数据率SDRAM，一个时钟周期内读写两次数据

## 主存储器

供处理器读写信息的存储器，存放程序和数据

* 存储位：二进制位，存储的最基本单元
* 内存单元：多个存储位组成一个内存单元，一般为字节（8位）
* 内存地址：内存单元的编号
* 地址位数：由最大的内存单元编号决定
* 字：CPU指令的操作单元，如32位、64位
* 字节顺序：多字节数据类型中字节的顺序
 * 大端序：高位在前，如Mac
 * 小端序：低位在前，如x86
* 纠错码：通过检验位自动纠错

### 高速缓存

* CPU对主存储器的访问总是局限在整个存储器的一小部分
* 访问存储器的某个字后，将该字和它的相邻单元从低速的大容量存储器系统中取到高速缓存中
* 指令和数据各用一个高速缓存以支持并行访问

![高速缓存](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/cache.png)

### 封装类型

* SIMM：单面接头内存条，单面有接线，共72根，每个时钟周期传输32位数据，已不常见
* DIMM：双面接头内存条，双面都有接线，每面120根，每个时钟周期传输64位数据，最常见的是DDR的DDIM

![DIMM](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/DIMM.png)

## 辅存储器

永久存储数据

### 磁盘

HDD，由一个或多个表面涂有磁性材料的铝制浅盘组成

* 磁道：磁盘旋转一周后写入磁盘的数据位形成的环形
* 扇区：每个磁道可以划分为固定长度的扇区
 * 前导区：读写之前对磁头进行同步
 * 数据区：一般存放512字节或4KB的数据
 * 纠错码：用于对数据纠错
* 隔离带：两个扇区之间的隔离区
* 柱面：位于同一半径的磁道集合

![磁盘](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/disk.png)

#### 磁盘读写

以扇区为单位进行读写

* 寻道：将磁盘臂轴向移动到相应半径的位置
* 旋转延时：等待相应的扇区旋转到磁头下方
* 读：磁头通过磁性区域时，将感应出正电流或负电流
* 写：磁头中有正负电流通过时，会磁化磁头正下方的磁盘表面

![4磁盘结构](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/disk2.png)

#### 磁盘控制器

控制磁盘驱动器的芯片

* 接受软件发出的读、写、格式化等命令
* 控制磁盘臂的运动，发现和纠正错误
* 将从内存中读来的8位字节转换为位串写入磁盘中或将磁盘中读出的位串转换为8位字节数据

#### 磁盘接口

连接磁盘和主机

* IDE盘：采用并行ATA接口，价格低廉、兼容性强，多用于家用产品
* STAT盘：采用串行ATA接口，传输速度更快
* SCSI盘：采用SCSI接口，传输速度高，多用于服务器市场

#### 廉价磁盘冗余阵列

RAID，通过多个磁盘并行读写提高磁盘性能

* RAID 0：磁盘按带划分，以交叉循环的方式将数据写入带中
* RAID 1：RAID 0 + 备份盘
* RAID 2：以数据字+校验码为单位写入磁盘中，每个磁盘一位
* RAID 3：RAID 2的简化，使用1位检验码
* RAID 4：RAID0 + 检验带
* RAID 5：将检验带均匀分布在所有驱动器上

![RAID](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/RAID.png)

### 固盘

SSD，由高速且非易失的Flash存储器制造

* 使用Flash作为存储介质
* 通过SSD控制器进行寻址、数据读写
* 通过对浮栅层充放电子，对块进行写入和擦除

![SSD](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/SSD.png)

### 光盘

#### 只读光盘

CD-ROM，通过在涂有玻璃表层的主盘上，用高能红外激光束烧出0.8mm直径的小孔制成，大容量、价格低廉，广泛用于分发软件、书籍、电影等数据

* 使用凹区、凸区之间的转换表示0和1
* 通过激光束经过凹区、凸区的反射光不同来读取数据

![CD-ROM](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/CD-ROM.png)

数据格式

* 14位表示一个符号，包括1字节数据，其他位用于符号内纠错
* 42个符号表示一个帧，包括24字节数据，其他位用于帧内纠错
* 98个帧表示一个扇区，包括16字节前导区、2048字节数据、288字节纠错码

![CD-ROM-data](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/CD-ROM-data.png)

#### 可刻光盘

CD-R，用于刻盘机进行刻录的光盘

* 刻写时激光照射到染料上产生一个黑点
* 读取时光接收器可以分辨出黑点和透明区，对应凹区、凸区

![CD-R](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/CD-R.png)

#### 可擦写光盘

CD-RW，使用合金作为记录层

* 高能激光下，合金熔化并从高反射性的晶态转换为低反射性的非晶态
* 中能激光下，合金熔化并重新转换位本来的晶态
* 低能激光下，合金不转换，用于读取数据

#### DVD

由激光二极管照射的凹区、凸区组成，通过光接收器读入信息

* 凹区更小，螺旋线更紧凑，容量更大
* 使用红色激光，有单面单层、单面双层、双面单层、双面双层四种格式

![DVD](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/DVD.png)

#### 蓝光

使用蓝色激光，更精确聚焦，能分辨出更小的凹区、凸区

## 输入输出设备

由I/O控制器和设备本身组成，用于计算机和外部世界交换信息，通过总线和处理器、存储器相连

* 输入时设备产生数据，存储到I/O控制器中，控制器向CPU发送中断请求，CPU挂起当前运行的程序，执行相应的中断程序，从控制器中取出数据交给上层程序
* 输出时上层程序产生数据，通过总线传输到控制器中进行输出

## 总线

用于连接CPU、存储器、输入输出设备，传输信号

* 总线仲裁器：决定谁来使用总线，输入输出设备的优先级高于CPU

### ISA总线

采用一条总线相连

![ISA-bus](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/ISA-bus.png)

### PCI总线

速度更快，提高了并行传输的位数

* CPU通过专用的高速连线与内存相连
* 其他外部设备直接连在PCI总线上

![PCI](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCI.png)

### PCIe总线

采用串行传输和包交换的点到点网络，传输方式更接近互联网

![PCIe](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCIe.png)

## 输入

### 键盘

分为机械式和电磁式

* 键盘按下时向CPU发送中断请求，CPU启动操作系统中的键盘中断响应程序，从键盘控制器中读取按下键的序号
* 键盘松开时会产生另外一个中断请求，用于处理多键组合的情况

### 触摸屏

* 红外型：在屏幕框的左边沿和上边沿装有红外发射装置，对应的右边沿和下边沿装有接收装置，当有物体挡住光线时，接收器感应这个信号
* 电阻型：由两层组成，上层布置水平导线，下层布置垂直导线，当被按压时，上层水平导线会接触下层垂直导线，连接在导线上的感应器可以感应这个信号
* 电容型：网格状的垂直分布的电容，当手指触摸屏幕时，触摸点的电容会发生变化

### 鼠标

鼠标移动一个最小位移单位时，向计算机发送三个字节的串，第一个字节为x方向的位移，第二个字节为y方向的位移，第三个字节为按键状态

* 机械式：底部有两个轴互相垂直的橡胶轮，滚动时轮子驱动的可变电阻发生变化
* 光学式：底部有LED光源照亮鼠标下面的表面，用1个摄像头拍摄小范围的图片，通过对连续图片的比较获得鼠标移动的距离
* 光电式，底部有一个两轴垂直的小球，移动时通过计算进入检测器的光脉冲数获得鼠标移动的距离

## 输出

### 液晶显示器

LCD，由两块平行的玻璃中间夹着一层密封的液晶组成

* 偏光板：产生液晶所需的偏振光
* 电极：给各个像素点施加电压，产生电场，控制该像素点的光通过情况
 * 黑白色：有光、无光
 * 全彩色：由RGB（红、绿、蓝）三种各有256级亮度的光组成发射光彩色图像
 * 索引色：对应调色板中256种颜色的某一种

![LCD](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/LCD.png)

### 打印机

#### 激光打印机

* 激光束经八面镜反射后照射在硒鼓表面的感光材料上，生成亮点和暗点，形成一条打印线
* 打印线经过碳粉盒时会吸附碳粉，遇到打印纸时碳粉会传给打印纸
* 打印纸通过高温滚筒的烘烤，碳粉被永久熔化在纸面上
 * 过渡色：将图像的点分解成6×6的像素点，通过其中黑色像素的数目表示灰色
 * 彩色：使用CYM（青、黄、品红）三种墨水组成反射光彩色图像

![激光打印机](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/laser-printer.png)

#### 喷墨打印机

使用可移动的打印头，上面带有墨水盒，通过皮带带动水平扫过纸张，墨滴从喷嘴喷出完成打印

* 压电式：喷嘴上有个压电陶瓷，加上电压时，会发生形变，将墨滴从墨嘴中挤出
* 热敏式：喷嘴里有个电阻，通电时加热墨汁产生气泡，将墨滴从墨嘴中挤出

墨水

* 染料墨水：将染料溶解在液体中，颜色鲜艳、不易阻塞，在强光下易褪色
* 色素墨水：将固体小颗粒的色素悬浮在液体中，不会褪色，容易阻塞喷嘴

## 电信设备

### 调制解调器

* 调制：基带信号转换为载波信号，从计算机中以方波信号的形式逐位接收信号，然后用调幅、调频或调相方式以一位或两位一组的形式发送
* 解调：载波信号转换为基带信号

![调整解调器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/modulation.png)

* 波特率：每秒载波信号变化的次数
* 比特率：每秒传输的二进制位数，为波特率的倍数

### DSL

数字用户线，也称为宽带，使用多个信号通道

#### ADSL

非对称数字用户线，扩展电话线频道以接入数字信号，将电话线的1.1MHz的频谱划分为256个独立频道，每个频道4312.5Hz

* 频道0：POTS，无格式的语音服务
* 频道1-5：不用，保证语音信号和数据信号互不干扰
* 频道6-37：32个上行频道
* 其他频道：下行频道

![ADSL频道](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/ADSL-Channel.png)

![ADSL](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/ADSL.png)

#### HFC

光纤同轴混合网，有线电视接入，扩展有线电视频道以接入数据信号

* 5-42MHz：上行频道
* 54-88MHz：TV，电视频道
* 88-108MHz：FM，无线广播频道
* 108-550MHz：TV，电视频道
* 550MHz-750MHz：下行频道

![HFC频道](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/HFC-Channel.png)

![HFC](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/HFC.png)

## 数码相机

使用光敏CCD阵列代替传统的胶片

* 当光线照射到CCD时，会产生电荷，通过模数转换，可以将电荷量转换为0-255（抵挡相机）或0-4095（高档相机）的整数
* 使用4个CCD组合起来表示一个像素点，上方放置Bayer过滤器，4个CCD分别记录一个红光、一个蓝光、两个绿光数值

![数码相机](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/digital-camera.png)

### 工作流程

* 自动聚焦：通过分析图像中的高频信息，移动镜头，直到使图像中的高频信息最大、图像最清晰
* 调整曝光度：通过测量照射到CCD的光线强度，调整镜头的光圈和曝光时间，使光线强度正好适合CCD的表示范围
* 设定白平衡：测量入射光线的波谱，以便后期进行颜色修正
* 后处理：进行颜色修正补偿、降噪、锐化等处理
* 压缩：对图像进行压缩，以降低对存储容量的要求，常用格式为JPEG
* 存储：将图像写入到存储介质中保存
