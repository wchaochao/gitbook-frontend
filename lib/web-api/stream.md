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
* internal queue: 内部队列，存储写入的chunk
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

## UnderlyingSource接口

```javascript
dictionary UnderlyingSource {
  ReadableStreamType type;
  unsigned long long autoAllocateChunkSize;

  UnderlyingSourceStartCallback start;
  UnderlyingSourcePullCallback pull;
  UnderlyingSourceCancelCallback cancel;
}

enum ReadableStreamType {
  "bytes"
}

callback UnderlyingSourceStartCallback = any (ReadableStreamController controller)
callback UnderlyingSourcePullCallback = Promise<void> (ReadableStreamController controller)
callback UnderlyingSourceCancelCallback = Promise<void> (optional any reason)

typedef (ReadableStreamDefaultController or ReadableByteStreamController) ReadableStreamController
```

### 特征属性

* type：source类型
 * undefined：普通source
 * bytes：字节source
* autoAllocateChunkSize：chunk大小

### 回调方法

* start(controller)：开始回调，可读流创建时触发
* pull(controller): 传送chunk回调，可读流desired size大于0时触发
* cancel(controller): 取消回调，可读流取消时触发

## ReadableStream接口

```javascript
interface ReadableStream {
  constructor(optional object underlyingSource, optional QueuingStrategy strategy = {});

  readonly attribute boolean locked;

  ReadableStreamReader getReader(optional ReadableStreamGetReaderOptions options = {});

  Promise<void> pipeTo(WritableStream destination, optional StreamPipeOptions options = {})
  ReadableStream pipeThrough(ReadableWritablePair transform, optional StreamPipeOptions options = {})

  Promise<void> cancel(optional any reason);
  sequence<ReadableStream> tee();

  async iterable<any>(optional ReadableStreamIteratorOptions options = {})；
}

dictionary ReadableStreamGetReaderOptions {
  ReadableStreamReaderMode mode;
}

enum ReadableStreamReaderMode { "byob" }

typedef (ReadableStreamDefaultReader or ReadableStreamBYOBReader) ReadableStreamReader

directionary StreamPipeOptions {
  boolean preventAbort = false;
  boolean preventCancel = false;
  boolean preventClose = false;

  AbortSignal signal;
}

dictionary ReadableWritablePair {
  required ReadableStream readable;
  required WritableStream writable;
}
```

### 内部属性

* [[state]]：可读流状态
 * readable：可读
 * closed：已关闭
 * errored：发生错误
* [[disturbed]]：可读流是否被读取或被取消
* [[readableStreamController]]：可读流控制器
* [[reader]]：可读流读取器
* [[storedError]]：可读流错误

### new ReadableStream(underlyingSource, strategy)

创建可读流
