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
* autoAllocateChunkSize：自动分配的chunk大小

### 回调方法

* start(controller)：开始读取回调，可读流创建时触发
* pull(controller): 读取chunk回调，可读流读取chunk时触发
* cancel(controller): 取消读取回调，可读流取消时触发

## ReadableStreamDefaultController接口

```javascript
interface ReadableStreamDefaultController {
  readonly attribute unrestricted double? desiredSize;

  void enqueue(optional any chunk);
  void error(optional any e);
  void close();
}
```

### 内部属性

| 内部属性 | 类型 | 描述 |
| --- | --- | --- |
| [[controlledReadableStream]] | ReadableStream | 控制器对应的可读流 |
| [[queue]] | list<(value, size)> | 内部队列 |
| [[queueTotalSize]] | Number | 内部队列中所有chunk的尺寸 |
| [[strategyHWM]] | Number | high water mark |
| [[strategySizeAlgorithm]] | (chunk) -> Number | 计算chunk的尺寸 |
| [[started]] | Boolean | source是否已执行完start回调 |
| [[pullAlgorithm]] | () -> Promise | 从source中读取chunk |
| [[pulling]] | Boolean | 是否正在读取chunk |
| [[pullAgain]] | Boolean | 是否继续读取下一个chunk |
| [[cancelAlgorithm]] | (reason) => Promise | 取消读取 |
| [[closeRequested]] | Boolean | 是否已结束读取 |

### 抽象操作

#### ReadableStreamDefaultControllerEnqueue(controller, chunk)

chunk进入队列

```
1. stream有ReadRequest，将chunk交给ReadRequest处理
2. stream没有ReadRequest，使用controller的[[strategySizeAlgorithm]]计算chunk尺寸，更新controller的[[queue]]和[[queueTotalSize]]
3. 从source中读取下一个chunk
```

#### ReadableStreamDefaultControllerShouldCallPull(controller)

是否可以从source中读取chunk

```
1. controller的[[closeRequested]]为false，stream的[[state]]为readable
2. controller的[[started]]为true
3. stream有ReadRequest或controller的desiredSize大于0
```

#### ReadableStreamDefaultControllerCallPullIfNeeded(controller)

从source中读取chunk

```
1. 不可以从source中读取chunk时，直接返回
2. controller的[[pulling]]为false时，设置[[pulling]]为true，执行controller的[[pullAlgorithm]]
  a. 成功时，重置controller的[[pulling]]为false，controller的[[pullAgain]]为true时，重置[[pullAgain]]为false并读取下一个chunk
  b. 失败时，控制器抛出错误
3. controller的[[pulling]]为true时，设置controller的[[pullAgain]]为true
```

#### ReadableStreamDefaultControllerError(controller, e)

controller抛出错误

```
1. 重置内部队列，controller的[[queue]]重置为空list，[[queueTotalSize]]重置为0
2. 清除controller算法，controller的[[strategySizeAlgorithm]]、[[pullAlgorithm]]、[[strategySizeAlgorithm]]重置为undefined
3. stream抛出错误
```

#### ReadableStreamError(stream, e)

stream抛出错误

```
1. stream的[[state]]设置为errored，[[storedError]]设置为e
2. reader有ReadRequest时，执行ReadRequest的error step，ReadRequest队列置空
3. reader的[[closedPromise]] reject e
```

#### ReadableStreamDefaultControllerClose(controller)

controller关闭

```
1. controller的[[closeRequested]]设置为true
2. controller的[[queue]]为空时，清除controller算法，执行stream关闭
```

#### ReadableStreamClose(stream)

stream关闭

```
1. stream的[[state]]设置为closed
2. reader有ReadRequest时，执行ReadRequest的close step，ReadRequest队列置空
3. reader的[[closedPromise]] resolve undefined
```

#### ReadableStreamCancel(stream, reason)

stream取消

```
1. stream的[[disturbed]]设置为true
2. 执行stream关闭
3. 执行controller的[[CancelSteps]]
```

### 内部方法

#### [[pullSteps]](readRequest)

reader读取chunk时的controller操作

```
1. controller的[[queue]]为空时，将readRequest存储到reader的队列中，cotnroller从source中读取chunk
2. controller的[[queue]]不为空时，从队列中取出chunk交给readRequest处理
  a. controller的[[closeRequested]]为true且[[queue]]为空时，清除controller算法，执行stream关闭
  b. 其他情况从source中读取下一个chunk
```

#### [[CancelSteps]](reason)

流取消时的controller操作

```
1. 重置内部队列
2. 清除controller算法
3. 执行controller的[[cancelAlgorithm]]操作
```

### 特征属性

* desiredSize：内部队列的desired size

### 特征方法

* enqueue(chunk): chunk进入队列
* error(e): controller抛出错误
* close(): controller关闭

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
