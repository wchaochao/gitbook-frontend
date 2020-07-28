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
* autoAllocateChunkSize：字节source自动分配的chunk大小

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
| [[controlledReadableStream]] | ReadableStream | 所属的可读流 |
| [[queue]] | list<(value, size)> | 内部队列 |
| [[strategySizeAlgorithm]] | (chunk) -> Number | 计算chunk的尺寸 |
| [[queueTotalSize]] | Number | 内部队列中所有chunk的尺寸 |
| [[strategyHWM]] | Number | high water mark |
| [[started]] | Boolean | source是否已执行完start回调 |
| [[pullAlgorithm]] | () -> Promise | 从source中读取chunk |
| [[pulling]] | Boolean | 是否正在读取chunk |
| [[pullAgain]] | Boolean | 是否继续读取下一个chunk |
| [[cancelAlgorithm]] | (reason) => Promise | 取消读取 |
| [[closeRequested]] | Boolean | 是否已结束读取 |

### 内部方法

#### [[pullSteps]] (readRequest)

default reader读取chunk时default controller的操作

```
1. controller.[[queue]]为空时
  a. read request存储到reader.[[readRequests]]中
  b. controller从source中读取下一个chunk
2. controller.[[queue]]不为空时，从队列中取出chunk交给read request的chunks step处理
  a. controller.[[closeRequested]]为true且[[queue]]为空时，重置controller，关闭stream
  b. 其他情况从source中读取下一个chunk
```

#### [[CancelSteps]] (reason)

流取消时的default controller操作

```
1. 重置controller
3. 执行controller的[[cancelAlgorithm]]
```

### 抽象操作

#### ReadableStreamDefaultControllerShouldCallPull(controller)

default controller是否可以从source中读取chunk

```
1. controller.[[closeRequested]]为false，controller.[[started]]为true
2. stream.[[state]]为readable
3. stream有read request或controller的desiredSize大于0
```

#### ReadableStreamDefaultControllerCallPullIfNeeded(controller)

default controller从source中读取chunk

```
1. 不可以从source中读取chunk时，直接返回
2. controller.[[pulling]]为false时，设置[[pulling]]为true，执行controller的[[pullAlgorithm]]
  a. 成功时，重置controller.[[pulling]]为false，controller.[[pullAgain]]为true时，重置[[pullAgain]]为false并读取下一个chunk
  b. 失败时，controller抛出错误
3. controller.[[pulling]]为true时，设置controller.[[pullAgain]]为true
```

### 特征属性

* desiredSize：内部队列的desired size

### 特征方法

#### enqueue(chunk)

chunk进入default controller队列

```
1. stream有read request，将chunk交给read request处理
2. stream没有read request，使用controller的[[strategySizeAlgorithm]]计算chunk尺寸，更新controller的[[queue]]和[[queueTotalSize]]
3. 从source中读取下一个chunk
```

#### error(e)

default controller抛出错误

```
1. 重置controller
  a. 重置内部队列，controller的[[queue]]重置为空list，[[queueTotalSize]]重置为0
  b. 清除controller算法，controller的[[strategySizeAlgorithm]]、[[pullAlgorithm]]、[[cancelAlgorithm]]重置为undefined
2. stream抛出错误
```

#### close()

default controller关闭

```
1. controller.[[closeRequested]]设置为true
2. controller.[[queue]]为空时，重置controller，执行stream关闭
```

## ReadableStreamDefaultReader接口

```javascript
interface ReadableStreamDefaultReader {
  constructor(ReadableStream stream);

  readonly attribute Promise<void> closed;

  Promise<ReadableStreamDefaultReadResult> read();
  Promise<void> cancel(optional any reason);
  void realseLock();
}

dirtionary ReadableStreamDefaultReadResult {
  any value;
  boolean done;
}
```

### 内部属性

| 内部属性 | 类型 | 描述 |
| --- | --- | --- |
| [[ownerReadableStream]] | ReadableStream | 所属的可读流 |
| [[closedPromise]] | Promise | 可读流closed的promise |
| [[readRequests]] | `list<ReadRequest>` | 读取请求列表 |

read request结构

* chunk steps：可读流读取chunk的回调
* close steps：可读流关闭时的回调
* error steps：可读流错误时的回调

### new ReadableStreamDefaultReader(stream)

创建可读流的默认读取器

```
1. stream锁定（已有读取器）时，抛出TypeError
2. 创建reader对象，初始化属性
  a. reader.[[ownerReadableStream]]为stream，stream.[[reader]]为reader
  b. reader.[[closedPromse]]根据stream.[[state]]设置
  c. reader.[[readRequests]]为空list
```

### 特征属性

* closed：reader的[[closedPromise]]

### 特征方法

#### read()

读取chunk

```
1. 创建promise和read request
  a. chunk steps为(chunk) => resolve({value: chunk, done: false})
  b. close steps为() => resolve({value: undefined, done: true})
  c. error steps为(e) => reject(e)
2. stream.[[disturbed]]设为true
3. stream.[[state]]为readable时，执行controller的[[PullSteps]]方法
4. stream.[[state]]为closed时，执行read request的close steps
5. stream.[[state]]为errored时，执行read request的error steps
```

#### cancel(reason)

取消读取

```
1. 执行stream取消
```

#### releaseLock()

取消锁定

```
1. reader.[[readRequests]]不为空时，抛出TypeError
2. reader.[[closedPromise]] reject TypeError
3. reader.[[ownerReadableStream]]设为undefined，stream.[[reader]]设为undefined
```

## ReadableByteStreamController接口

```javascript
interface ReadableByteStreamController {
  readonly attribute unrestricted double? desiredSize;
  readonly attribute ReadableStreamBYOBRequest? byobRequest;

  void enqueue(ArrayBufferView chunk);
  void error(optional any e);
  void close();
}
```

### 内部属性

| 内部属性 | 类型 | 描述 |
| --- | --- | --- |
| [[controlledReadableStream]] | ReadableStream | 所属的可读流 |
| [[queue]] | a list of readable byte stream queue entries | 内部队列 |
| [[autoAllocateChunkSize]] | Number | chunk的尺寸 |
| [[queueTotalSize]] | Number | 内部队列中所有chunk的尺寸 |
| [[strategyHWM]] | Number | high water mark |
| [[started]] | Boolean | source是否已执行完start回调 |
| [[pullAlgorithm]] | () -> Promise | 从source中读取chunk |
| [[pulling]] | Boolean | 是否正在读取chunk |
| [[pullAgain]] | Boolean | 是否继续读取下一个chunk |
| [[pendingPullIntos]] | a list of pull-into descriptors | pull into 描述列表 |
| [[byobRequest]] | ReadablsStreamBYOBRequest | 第一个pull into 描述对应的byob request |
| [[cancelAlgorithm]] | (reason) => Promise | 取消读取 |
| [[closeRequested]] | Boolean | 是否已结束读取 |

queue entry结构

* buffer：view的buffer
* byte offset：view的offset
* byte length：view的length

pull-into descriptor结构

* buffer：view的buffer
* byte offset：view的offset
* byte length：view的length
* bytes filled：view已写入的字节数
* element size：view每个单位的字节数
* view constructor：view构造器
* reader type：读取器类型，default或byob

### 内部方法

#### [[pullSteps]] (readRequest)

default reader读取chunk时byob controller的操作

```
1. controller.[[queue]]为空时
  a. autoAllocateChunkSize有值时，生成pull into descriptor，view constructor为Uint8Array，reader type为default，将descriptor追加到[[pendingPullIntos]]中
  b. read request存储到reader.[[readRequests]]中
  c. controller从source中读取下一个chunk
2. controller.[[queue]]不为空时，从队列中取出chunk转换为Uint8Array交给read request的chunks step处理
  a. controller.[[closeRequested]]为true且[[queue]]为空时，重置controller，关闭stream
  b. 其他情况从source中读取下一个chunk
```

#### [[CancelSteps]] (reason)

流取消时的default controller操作

```
1. 第一个descriptor的bytes filled不为0时，抛出TypeError
2. 重置controller
3. 执行controller的[[cancelAlgorithm]]
```

### 抽象操作

#### ReadableByteStreamControllerShouldCallPull(controller)

byob controller是否可以从source中读取chunk

```
1. controller.[[closeRequested]]为false，controller.[[started]]为true
2. stream.[[state]]为readable
3. stream有read request或read into request或controller的desiredSize大于0
```

#### ReadableByteStreamControllerCallPullIfNeeded(controller)

byob controller从source中读取chunk

```
1. 不可以从source中读取chunk时，直接返回
2. controller.[[pulling]]为false时，设置[[pulling]]为true，执行controller的[[pullAlgorithm]]
  a. 成功时，重置controller.[[pulling]]为false，controller.[[pullAgain]]为true时，重置[[pullAgain]]为false并读取下一个chunk
  b. 失败时，controller抛出错误
3. controller.[[pulling]]为true时，设置controller.[[pullAgain]]为true
```

#### ReadableByteStreamControllerProcessPullIntoDescriptorsUsingQueue(controller)

将queue中的chunk写入pull into描述列表中，写入完成后提供给reader

```
1. [[pendingPullIntos]]不为空且[[queueTotalSize]]大于0时，将queue中的chunk不断写入第一个descriptor中
  a. descriptor的bytes filled不足一个element size时， 直接返回
  b. descriptor的bytes filled为多个element size（最大为byte length / element size）时
    i. 取出descriptor，转换为ArrayBufferView，length为bytes filled / element size
    ii. descriptor的reader type为default时，将ArrayBufferView提供给第一个read request处理（closed时使用close steps处理）
    iii. descriptor的reader type为byob时，将ArrayBufferView提供给第一个read into request处理
  c. 继续写入下一个descriptor（closed时使用close steps处理）
```

### 特征属性

* desiredSize：内部队列的desired size

#### byobRequest

第一个pull into 描述对应的byob request

```
1. controller.[[byobRequest]]不存在且controller.[[pendingPullIntos]]不为空时
  a. 获取[[pendingPullIntos]]中的第一个pull descriptor
  b. 创建Uint8ArrayView，buffer为descriptr的buffer，offset为descriptor的byte offset加上bytes filled，length为descriptor的byte length减去bytes.filled
  c. 创建byob request，[[controller]]为controller，[[view]]为Unit8ArrayView，设置controller.[[byobRequest]]为byob request
2. 返回controller.[[byobRequest]]
```

### 特征方法

#### enqueue(chunk)

chunk进入byob controller队列

```
1. reader不存在时，将chunk追加到内部队列中
2. reader为default reader时
  a. read requests为空，将chunk追加到内部队列中
  b. read requests不为空时，将chunk转换为Uint8Array，提供给第一个read request的chunk steps处理
3. reader为byob reader，将chunk追加到内部队列中，将queue中的chunk写入pull into描述列表中，写入完成后提供给reader
4. 拉取下一个chunk
```

#### error(e)

byob controller抛出错误

```
1. 重置controller
  a. 重置内部队列，controller的[[queue]]重置为空list，[[queueTotalSize]]重置为0
  b. 清除controller算法，controller的[[[pullAlgorithm]]、[[cancelAlgorithm]]重置为undefined
2. 清除[[pendingPullIntos]]队列
3. stream抛出错误
```

#### close()

byob controller关闭

```
1. controller.[[queue]]不为空时，[[closeRequested]]设置为true，直接返回
2. controller.[[queue]]为空时
  a. 第一个descriptor的bytes filled大于0时，抛出TypeError
  b. 重置controller，执行stream关闭
```

## ReadableStreamBYOBRequest接口

```javascript
interface ReadableStreamBYOBRequest {
  readonly attribute ArrayBufferView? view;

  void respond(unsigned long long bytesWritten);
  void respondWithNewView(ArrayBufferView view);
}
```

### 内部属性

| 内部属性 | 类型 | 描述 |
| --- | --- | --- |
| [[controller]] | ReadableByteStreamController | 所属的byob控制器 |
| [[view]] | TypedArray | 控制器写入数据区域 |

### 特征属性

* view：byob request的[[View]]

### 特征方法

#### respond(byteswritten)

写入数据后响应

```
1. stream.[[state]]为readable时，第一个descriptor的bytes filled加上byteswritten
  a. bytes filled小于element size时，直接返回
  b. bytes filled大于element size时
    i. 余数部分对应的chunk加入到内部队列中
    ii. 整数部分队员的chunk转换为ArrayBufferView，提供给reader
    iii. 将queue中的chunk写入pull into描述列表中，写入完成后提供给reader
2. stream.[[state]]为closed时
  a. bytesWritten不为0时，抛出TypeError
  b. 将pull into descriptor转换为ArrayBufferView，提供给相应的read into request的closed step处理
```

### respondWithNewView(view)



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

### 抽象操作

#### ReadableStreamError(stream, e)

stream抛出错误

```
1. stream.[[state]]设置为errored，[[storedError]]设置为e
2. reader为default reader时，遍历[[readRequests]]，执行error steps，执行完后[[readRequests]]置空
2. reader为byob reader时，遍历[[readIntoRequests]]，执行error steps，执行完后[[readIntoRequests]]置空
3. reader的[[closedPromise]] reject e
```

#### ReadableStreamClose(stream)

stream关闭

```
1. stream.[[state]]设置为closed
2. reader为default reader时，遍历[[readRequests]]，执行close steps，执行完后[[readRequests]]置空
3. reader的[[closedPromise]] resolve undefined
```

#### ReadableStreamCancel(stream, reason)

stream取消

```
1. stream.[[disturbed]]设置为true
2. 执行stream关闭
3. 执行controller的[[CancelSteps]]
```

### new ReadableStream(underlyingSource, strategy)

创建可读流
