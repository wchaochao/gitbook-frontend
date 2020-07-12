# Streams

标签（空格分隔）： 前端知识体系

---

## 基本概念

### Readable stream

可读流

* underlying source: 数据源，提供chunk
 * push source: 推送资源
 * pull source: 拉取资源
* internal queue: 内部队列，存储source提供的chunk
* reader: 读取器，从queue中读取chunk
* consumer: 消费者，消费读取的chunk
* cancel: 取消读取，丢弃queue中还存在的chunk

### Writable stream

可写流

* producer: 生产者，生产chunk
* writer: 写入器，将生产的chunk写入queue中
* internal queue: 内部队列，存储写入的queue
* undlying sink: 数据槽，queue中的chunk传送到sink中
* abort: 中断写入，丢弃queue中还存在的chunk

### Transform stream

转换流

* writable side: 写入侧，接收数据并进行转换
* readable side: 读取侧，读取转换后的数据

### Pipe

管道

* pipeTo: 将可读流的数据写入可写流中
* pipeThrough: 将可读流的数据传送到转换流中进行转换

### Queue strategy

队列策略

* size: 每个chunk的大小
* high water mark：高压线标志
* desired size：期望的size，等于high water mark - 所有chunk的大小
* backpressure: 背压，desired size <= 0时会减缓读取或写入的速度

### Lock

锁定

* 可读流/可写流最多只能有一个读取器/写入器
* 绑定读取器/写入器后，流处于锁定状态，只有解锁后才能接受下一个读取器/写入器
