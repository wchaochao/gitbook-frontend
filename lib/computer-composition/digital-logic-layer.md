# 数字逻辑层

标签（空格分隔）： 前端知识体系

---

## 门

制造数字计算机的硬件基础

![门](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/gate.png)

### 逻辑值

数字电路只能表示两个逻辑值

* 0~0.5V表示一个值，如二进制0
* 1~1.5V表示另一个值，如二进制1

### 晶体管

作为一个快速二进制开关，有三个管脚：集极、基极、射极

* 基极的电压小于某个值时，晶体管是断开的，相当于一个无穷大的电阻
* 基极的电压大于某个值时，晶体管是连通的，相当于导线

### 非门

带一个二级晶体管的简单电路

* 输入电压低于某个值时，输出电压接近外接基准电压
* 输入电压高于某个值时，输出电压接地

| A | X |
| --- | --- |
| 0 | 1 |
| 1 | 0 |

![非门](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/NOT-gate.png)

### 与非门

两个晶体管串联

* V1、V2同时为高时，输出电压接地
* 其他情况输出电压接近外接基准电压

| A | B | X |
| --- | --- | --- |
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

![与非门](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/NAND-gate.png)

### 或非门

两个晶体管并联

* V1、V2有一个为高电压时，输出电压接地
* 其他情况输出电压接近外接基准电压

| A | B | X |
| --- | --- | --- |
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 0 |

![或非门](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/NOR-gate.png)

### 与门

与非门串联一个非门组成，都为真时才为真

| A | B | X |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

### 或门

或非门串联一个非门组成，有一个为真时为真

| A | B | X |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

### 异或门

不同时为真

| A | B | X |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

## 布尔代数

输入和输出都只能是0或1

### 真值表

表格的每一行描述一种输入组合对应的函数值

* n个变量的布尔函数的输入有2^n种组合
* n个变量的布尔函数的结果可以用2^n位二进制值表示

### 布尔公式

使用哪些输入组合可以使函数值为1来描述布尔函数

* 输入变量上面加一横线表示非
* 隐含的乘号或小圆点表示与
* 加号表示或

![布尔公式](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/boolean-formula.png)

### 布尔电路

使用布尔电路实现布尔函数

1. 写出该函数的真值表
2. 根据真值表得到布尔公式
3. 根据布尔公式得到布尔电路

![布尔函数](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/boolean-circuit.png)

### 布尔恒等式

每个等式有两种对称的形式，将与或及01互换，可以得到另一种形式

![布尔恒等式](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/boolean-identity.png)

根据恒等式，可以将门进行转换

![门转换1](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/gate-convert-1.png)

![门转换2](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/gate-convert-2.png)

## 基本数字逻辑电路

以多个门组成的模块为基本组件

### 组合逻辑电路

有多个输入信号和多个输出信号，且输出信号由输入信号决定

#### 译码器

根据输入信号选中某个输出信号，有n个输入信号，2^n个输出信号

* 每种输入信号的组合都会有对应的输出信号为1，其他为0

![译码器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/decoder.png)

#### 多路选择器

根据控制信号选择某个输入，有n个控制信号，2^n个输入信号，1个输出信号

* 每种控制信号的组合都会筛选出对应的输入信号
* 输入信号确定时，相当于一个确定的真值表
* 通过遍历控制信号的组合，可以将并行信号转换为串行信号

![多路选择器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Multiplexer.png)

#### 比较器

比较输入的两个字，有2n个输入信号，1个输出信号

* 输入信号两两相同时，输出为1，否则为0

![比较器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Comparator.png)

### 算术电路

#### 移位器

左移或右移，有1个控制信号，n个输入信号，n个输出信号

* 控制信号为0时左移，1时右移，空位补0

![移位器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Shifter.png)

#### 半加器

两个1位数相加产生和、进位

![半加器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/half-adder.png)

#### 全加器

两个1位数、低位进位相加产生和、高位进位

![全加器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/full-adder.png)

#### 加法器

多个全加器相连

* 行波进位加法器：从低位往高位依次全加运算
* 进位选择加法器：低位和高位并行全加运算，然后根据低位产生的最终进位，选择一个高位全加运算结果

#### 算术逻辑单元

完成两个机器字的与、或、非、求和运算

* 根据F0、F1选择信号进行与、或、非、求和运算
* ENA为0时会强制A为0
* ENB为0时会强制B为0
* INVA为1时会对A求反

![算术逻辑单元](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/ALU.png)

### 时钟

一种特定的电路，发出一系列的脉冲，脉冲的宽度相同，两个连续脉冲之间的间隔也相同

* 时钟周期：两个连续脉冲之间相应边沿的时间间隔
* 子时钟周期：在主时钟周期上接出一个信号，延迟一个已知的延时，用于处理同一时钟周期的事件顺序
* 非对称时钟：时钟信号在高位和低位的时间不同

![时钟](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/clock.png)

### 集成电路

在一小块方形的硅片上集成多个门电路

* DIP：双排直插式封装
* PGA：引脚阵列封装
* LGA：触点阵列封装

![IC](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/IC.png)

### 现场可编程门阵列

FPGA，包含可编辑逻辑的芯片

* 由查找表LUT和可编程内部连线PI组成
* 通过配置数据可以转变成任意功能的逻辑电路，用于产品的原型设计

## 内存

用来存放计算机执行的指令和执行指令用到的数据

### 锁存器

记录上一次输入的值，采用电平触发

#### SR锁存器

输入信号S、R，输出信号Q、Q反

* S=R=0时，输出信号Q可以为0和1，锁存器状态不变
* S=1，R=0时，输出信号Q只能为1，设置锁存器
* S=0，R=1时，输出信号Q只能为0，重置锁存器
* S=R=1时，输出信号Q、Q反都为0，当输入都变为0时，输出将由后一个变为0的输入决定

![SR锁存器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Latches.png)

#### 时钟SR锁存器

添加一个时钟作为额外的输入

* 时钟信号为0时，不会改变锁存器状态
* 时钟信号为1时，锁存器对S和R的变化起反应

![时钟SR锁存器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/clock-latches.png)

#### 时钟D锁存器

使用1个输入防止出现S=R=1的情况，一位存储器

* 时钟信号为0时，不会改变锁存器状态
* 时钟信号为1时，锁存器存入D值

![时钟D锁存器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/D-Latches.png)

### 触发器

在特定时刻对某个信号采样并加以存储，采用边沿触发

#### 脉冲发生器

利用非门有个微小的、但不为0的传输延迟，在时钟上升沿时制造一个短脉冲

![脉冲发生器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/plus-generator.png)

#### D触发器

脉冲发生器加D锁存器

![D触发器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/D-trigger.png)

### 寄存器

多个触发器组合在一起组成寄存器，保存超过一位长的数据

![寄存器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/register.png)

### 三态器

可以输出0、1和非0非1（电路开路）三种状态

#### 非反向缓冲器

相当于开关，有1个输入、1个输出、1个控制信号

* 控制信号为1时，电路连通
* 控制信号为0时，电路断开

![非反向缓冲器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Not-inverting-buffer.png)

#### 反向缓冲器

非门和开关的复合，有1个输入、1个输出、1个控制信号

* 控制信号为1时，为非门
* 控制信号为0时，电路断开

![反向缓冲器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/inverting-buffer.png)

### 内存组成

为每个内存字进行编址

* 输入信号I0、I1、I2与所有D触发器的D相连
* 地址信号A0、A1通过译码器选择一个字
* 片选信号CS为0时，不能读取、不能写入
* 片选信号为1时
 * 读写信号RD为1，进行读操作，输出使能信号OE为1时可以输出
 * 读写信号RD为0，进行写操作，不能输出

![4×3存储器](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Memory.png)

### 内存芯片

对存储器字数和字位进行扩展，集成在一个芯片上，需要考虑输出的位数和地址如何确定

* 片选信号CS表示选中某个芯片进行读写
* 写使能信号WE表示进行写操作
* 输出使能信号OE表示驱动芯片数据输出
* 行地址信号RAS表示选中行
* 列地址信号CAS表示选中列
* 存储体信号Bank表示选中芯片中的某个存储体

![内存芯片](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/Memory-IC.png)

### RAM

随机访问存储器，可读写

* SRAM：静态RAM，内部采用类型D触发器的电路，只要不断电，就可以永久存储数据，用于高速缓存
* DRAM：动态RAM，使用晶体管和电容组成的存储单元来存储数据，由于电容中的电荷会泄露，需要不断的刷新，用于主存
 * FPM：快页DRAM，最老的DRAM，内部为存储位矩阵，通过行地址、列地址寻址
 * EDO：扩展数据输出DRAM，允许在前一个内存访问周期结束之前启动第二个内存访问周期
 * SDRAM：同步DRAM，静态和动态RAM的结合，统一由系统时钟驱动，省去了与内存芯片握手的控制信号
 * DDR SDRAM：双倍数据速率SDRAM，可在时钟的上升沿和下降沿均输出数据，使数据速率提高了一倍

### ROM

只读存储器，不能改变或擦除

* PROM：可编程ROM，可在现场编程（一次）
* EPROM：可擦除PROM，可以现场编程、现场擦除，通过紫外线全部擦除
* EEPROM：通过脉冲擦除某个字节
* 闪存：按块进行擦除和重写

## CPU

通过管脚与外部世界通信

### 管脚

通过总线和内存、输入/输出设备的相应管脚相连

* 地址信号：可以寻址2^m的地址空间
* 数据信号：一次读写n位的字
* 控制信号：调整进出CPU的数据流和时间
 * 总线控制信号：CPU给内存或输入/输出设备的信号
 * 中断信号：输入/输出设备给CPU的信号，要求CPU响应
 * 总线仲裁信号：控制总线流量，防止两个设备在同一时刻使用总线
 * 协处理器信号：和协处理器协作的信号，如浮点运算芯片、图形处理芯片
 * 状态信号：提供或接收状态信息
 * 其他控制信号：如电源、接地、时钟信号

![CPU管脚](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/CPU-Pin.png)

### Core i7

4核64位处理器

* 4核CPU，每个CPU最多可执行4个指令
* 三级高速缓存
 * 每个核都有的32KB一级数据高速缓存、32KB一级指令高速缓存、256KB二级高速缓存
 * 所有核共享的4~15M三级高速缓存
* 两条外部总线
 * DDR3内存总线用来访问DRAM构成的主存储器
 * PCIe总线用于和输入/输出设备通信
* LGA封装，1155个针脚，286个连接电源，360个接地，447个信号线
* 5个状态级别，在空闲时进入睡眠状态，减少耗电

![Core i7](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/core-i7.png)

#### Core i7 引脚

Intel公司的i7芯片，实现了x86指令集，面向桌面应用，高能耗高性能

* DDR3总线信号：提供给DRAM存储体的地址、数据、控制、时钟信号
* PCIe总线信号：每条串行链路构成一个和外部设备通信的信道
* DMI总线信号：直接媒体访问接口，连接伴随芯片组
 * P67芯片提供SATA、USB、声频、PCIe、Flash存储器接口
 * ICH10芯片提供旧接口支持，如PCI、中断控制器
* 电源管理信号：自动选择电源电压，管理不同的睡眠状态
* 散热管理信号：解决热量问题，检测芯片何时过热
* 时钟信号：提供系统时钟，进行分频和倍频，以生成所需的各类时钟

![Core i7 引脚](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/core-i7-bin.png)

#### DDR3总线流水

对同一DRAM芯片中的多个存储体进行并行访问，单个DDR3信道最多允许4个并发访问

* 总线时钟：协调所有总线活动
* 总线命令：指示所连接的DRAM进行何种操作
 * ACTIVATE命令：通过地址信号给出要访问的DRAM行地址，使它做好接收后续存储访问请求的准备
 * READ/WRITE命令：给出总线列地址，进行读、写操作
 * PRECHARGE命令：对存储体进行充电，做好下次进入ACTIVATE的准备
* 地址：DRAM存储地址
* 数据：传输的数据

![DDR3总线流水](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/DDR3-stream.png)

### OMAP4430

德州仪器的OMAP4430片上系统，实现了ARM指令集，面向移动应用，低能耗高性能

* 2个ARM A9核，每个A9核最多可执行2个指令
* 3个以上的加速处理器，可在较低能耗下提供更高的计算力
 * 图形处理器SGX540：与GPU类似，提供有效的可编程3D渲染
 * 图像信号处理器ISP：用于图像处理的可编程处理器
 * 视频信号处理器IVA3：视频编码、解码
* 二级高速缓存
 * 每个核都有的32KB一级数据高速缓存、32KB一级指令高速缓存
 * 所有核共享的1M二级高速缓存
* 使用LPDDR2接口与内存连接，采用流水方式并行访问内存
* PBGA封装，547个针脚
* 应用了许多能量管理手段
 * 动态电压调节：让组件在一个较低的电压下工作，大幅减少能量的需求
 * 门控电源技术：当组件不需要时，断掉电源

![OMAP4430](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/OMAP4430.png)

### ATmega168

Atmel公司的ATmega168片上，实现了AVR指令集，面向嵌入式应用，低价格多用途

* 存储系统集成在处理器里
 * 16KB的内部Flash存储器，存储应用程序
 * 1KB的SRAM，存储临时的数据变量
 * 1KB的EEPROM，存储系统的配置数据
* 28管脚，没有地址和数据线，仅需和设备连接
 * Port B/Port D：各8根管脚，数字输入/输出口，用来连接I/O设备
 * Port C：7根管脚，模拟输入/输出口

![ATmega168](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/ATmega168.png)

## 总线

计算机中多个设备公用的电子通道

* 主设备通过总线驱动器和总线相连
* 从设备通过总线接收器和总线相连
* 既能做主设备又能做从设备的设备通过总线转发器和总线相连
* 总线接口芯片通常是三态门，设备不需要和总线连接时和总线断开，需要时采用类似集电极开路的方式与总线连接

![多总线计算机系统](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/bus-system.png)

### 总线宽度

为减少管脚，会复用地址线和数据线

* 地址信号线：越多寻址空间越大，
* 数据信号线：越多数据带宽越大

### 总线时钟

#### 同步总线

有总线时钟，总线操作都占用整数个总线周期

![同步总线](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/sync-bus.png)

#### 异步总线

无总线时钟，总线操作总是尽快完成

![异步总线](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/async-bus.png)

### 总线仲裁

仲裁多个设备同时使用总线的情况

#### 集中式

总线控制器发现总线请求后，发起总线授权，授权给优先级高、距离近的设备

![集中式总线仲裁](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/central-bus-arbitration.png)

#### 竞争式

所有设备监听总线使用情况，总线空闲时由优先级高的设备使用总线

![竞争式总线仲裁](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/competitive-bus-arbitration.png)

### 总线操作

#### 成块传送

一次传送多个字

![成块传送](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/block-transfer.png)

#### 中断处理

由中断控制器统一处理

![中断处理](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/interrupt-controller.png)

### PCI总线

通过多条总线兼容旧设备

* PCI桥：连接CPU、内存、PCI总线
* ISA桥：连接PCI总线、ISA总线

![PCI总线结构](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCI-structure.png)

#### PCI总线信号

* 必备信号：用于读写操作
* 可选信号：用于64位和多处理器系统

![PCI总线信号](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCI-signal.png)

#### PCI总线事务

在时钟下降沿开启操作

![PCI总线事务](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCI-affair.png)

### PCIe总线

基于高速点到点串行连接设备，一个微型的包交换网络

* 两个DDR3总线，连接主存储器
* 一个16位PCIe总线或两个8位PCIe总线，连接图形卡
* 芯片P67提供多个现代高性能I/O接口，如PCIe、SATA、USB、以太网口、音频接口
* ICH10为老设备提供传统的接口支持

![PCIe总线结构](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCIe-structure.png)

#### PCIe协议栈

* 物理层：解决点到点连接上从发送方到接收方的数据位的传送
 * 每个点到点的连接由一对或多对单工的连接线组成
 * 没有主时钟，通过8b/10b编码同步
* 链路层：解决数据包的传输
 * 添加顺序号和CRC检错纠错码
* 事务层：处理总线事务
 * 将每个传输信道分解成最多8个虚电路，每个虚电路处理一种不同类型的流量
 * 根据数据包类型的不同，加上不同的标签，根据这些标签决定下一个要处理的数据包
* 软件层：操作系统与PCIe系统的接口

![PCIe协议栈](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PCIe-protocol.png)

## 输入输出接口

计算机通过输入输出接口与外部交换信息

### 类型

* 按通用性分类：通用接口、专用接口
* 按数据传送方式分类：串行接口、并行接口
* 按信号类型分析：数字接口、模拟接口

### 功能

* 设备识别和选择
* 数据缓冲和控制
* 控制命令和状态信息传递
* 数据转换和传输

### PIO接口

并行输入输出接口

* 地址信号用于选定端口A、B、C对应的数据寄存器和另一个配置寄存器
* 数据信号用于传递和接收数据
* 命令信号用于片选、读、写、重置

![PIO接口](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/PIO.png)

### USB接口

通用串行接口，将低速输入/输出设备连接到计算机

* 由一个插在主总线上的根集线器组成，根集线器的电缆插口可以连接输入/输出设备或扩展集线器
* 电缆中有四根导线，两根用于数据传输，一根用作电源，一根接地
* 当插入一个新的输入/输出设备时，根集线器检测到这个事件，并发出中断信号给操作系统，操作系统查询设备并识别，赋给设备一个唯一的地址（1~127），记录到设备内部的配置寄存器中

#### USB接口帧

* 支持四种类型的帧
 * 控制帧用于配置设备，对设备发出命令，查询设备状态
 * 同步帧用于需要以精确的时间间隔发送和接收数据的实时设备
 * 块传送帧用于对数据没有实时要求的设备
 * 中断帧用于支持中断
* 每个帧由一个或多个包组成，可能两个方向传输数据
 * 令牌包由根传送到设备，用于系统控制，如SOF（帧开始），IN（请求设备数据）、OUT（给设备的数据）、SETUP（配置设备）
 * 数据包用来双向传递数据，由SYN（同步）、PID（数据类型）、PAYLOAD（载荷）、CRC（纠错码）
 * 握手包包括三种，ACK（前面的数据包正确接收）、NAK（CRC错）、STALL（请稍候）

![USB总线](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-computer-composition/USB-frame.png)
