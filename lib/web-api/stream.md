# Streams

标签（空格分隔）： 前端知识体系

---

## 基本概念

### Readable stream

可读流

* underlying source: 数据源封装，负责从数据源中拿到chunk提供给queue
 * push source: 推送资源，如网络请求
 * pull source: 拉取资源，如文件读取
* internal queue: 内部队列，存储chunk
* reader: 读取器，从queue中读取chunk
* consumer: 消费者，消费读取的chunk
* cancel: 取消读取，丢弃queue中还存在的chunk

### Writable stream

可写流

* producer: 生产者，生产chunk
* writer: 写入器，将生产的chunk写入queue中
* internal queue: 内部队列，存储chunk
* underlying sink: 数据槽，queue中的chunk传送到sink中
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

## ReadableStream接口

```javascript
interface ReadableStream {
  constructor(optional object underlyingSource, optional QueuingStrategy strategy = {});

  readonly attribute boolean locked;

  ReadableStreamReader getReader(optional ReadableStreamGetReaderOptions options = {});
  Promise<void> cancel(optional any reason);

  Promise<void> pipeTo(WritableStream destination, optional StreamPipeOptions options = {})
  ReadableStream pipeThrough(ReadableWritablePair transform, optional StreamPipeOptions options = {})

  sequence<ReadableStream> tee();

  async iterable<any>(optional ReadableStreamIteratorOptions options = {})；
}

typedef (ReadableStreamDefaultReader or ReadableStreamBYOBReader) ReadableStreamReader

dictionary ReadableStreamGetReaderOptions {
  ReadableStreamReaderMode mode;
}

enum ReadableStreamReaderMode {
  "byob"
}

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
* [[controller]]：可读流控制器
* [[reader]]：可读流读取器
* [[storedError]]：可读流错误
* [[Detached]]：可读流是否被转让

### 抽象操作

#### ReadableStreamError(stream, e)

stream抛出错误

```
1. stream.[[state]]设置为errored，[[storedError]]设置为e
2. reader为default reader时，遍历[[readRequests]]，执行error steps，执行完后[[readRequests]]置空
3. reader为byob reader时，遍历[[readIntoRequests]]，执行error steps，执行完后[[readIntoRequests]]置空
4. reader的[[closedPromise]] reject e
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
2. stream.[[state]]为closed时，返回Promise.resolve(undefined)
3. stream.[[state]]为errored时，返回Promise.reject(stream.[[storedError]])
4. stream.[[state]]为readable时，执行stream关闭，执行controller的[[CancelSteps]]
```

### new ReadableStream(underlyingSource, strategy)

创建可读流

```
1. 创建ReadableStream对象，设置[[state]]为readable，[[disturbed]]为false
2. underlyingSource无type时，stream.[[controller]]为default controller
  a. controller.[[stream]]为stream
  b. controller.[[queue]]为空列表，[[queueTotalSize]]为0
  c. controller.[[strategySizeAlgorithm]]为strategy.size的封装（默认为() => 1），[[strategyHWM]]为strategy.highWaterMark
  d. controller的[[started]]、[[pulling]]、[[pullAgain]]、[[closeRequested]]为false
  e. controller.[[pullAlgorithm]]为underlyingSource.pull的封装（默认为(controller) => Promise<undefined>），[[cancelAlgorithm]]为underlyingSource.cancel的封装（默认为(controller) => Promise<undefined>）
  f. 执行underlysingSource.start（默认为() => undefined），结果封装为Promise
    i. 成功时，controller.[[started]]设为true，controller读取chunk
    ii. 失败时，controller抛出错误
3. underlyingSource.type为bytes，stream.[[controller]]为byob controller
  a. controller.[[stream]]为stream
  b. controller.[[queue]]为空列表，[[queueTotalSize]]为0
  c. controller.[[autoAllocateChunkSize]]为strategy.autoAllocateChunkSize，[[strategyHWM]]为strategy.highWaterMark
  d. controller的[[started]]、[[pulling]]、[[pullAgain]]、[[closeRequested]]为false
  e. controller.[[pullAlgorithm]]为underlyingSource.pull的封装（默认为(controller) => Promise<undefined>），[[cancelAlgorithm]]为underlyingSource.cancel的封装（默认为(controller) => Promise<undefined>）
  f. controller.[[pendingPullIntos]]为空列表，[[byobRequest]]为null
  f. 执行underlysingSource.start（默认为() => undefined），结果封装为Promise
    i. 成功时，controller.[[started]]设为true，controller读取chunk
    ii. 失败时，controller抛出错误
```

### 特征属性

* locked：是否有[[reader]]
 * 锁定后stream不能取消
 * 锁定后stream不能再生成reader

### 特征方法

#### cancel(reason)

流取消

```
1. 流锁定时返回Promise.resolve(TypeError)
2. 执行ReadableStreamCancel(stream, reason)
```

#### getReader(options)

生成reader

```
1. options.mode未提供时，获取default reader
  a. stream锁定时，抛出TypeError
  b. 创建ReadstreamDefaultReader对象，reader.[[stream]]为stream，stream.[[reader]]为reader
  c. reader.[[readRequests]]为空列表
  d. stream.[[state]]为readable时，reader.[[closePromise]]为new promise
  e. stream.[[state]]为closed时，reader.[[closePromise]]为Promise.resolve(undefined)
  f. stream.[[state]]为errored时，reader.[[closePromise]]为Promise.reject(stream.[[storedError]])
2. options.mode为byob时，获取byob reader
  a. stream锁定时，抛出TypeError
  b. controller不为byob controller时，抛出TypeError
  c. 创建ReadstreamDefaultReader对象，reader.[[stream]]为stream，stream.[[reader]]为reader
  d. reader.[[readIntoRequest]s]为空列表
  e. stream.[[state]]为readable时，reader.[[closePromise]]为new promise
  f. stream.[[state]]为closed时，reader.[[closePromise]]为Promise.resolve(undefined)
  g. stream.[[state]]为errored时，reader.[[closePromise]]为Promise.reject(stream.[[storedError]])
```

### 管道方法

#### pipeTo(destination, options)

写入可写流

```
1. source、destination锁定时，返回Promise.resolve(TypeError)
2. source生成reader，destination生成writer
3. source.[[disturbed]]设为true
4. reader从underlying source中读取chunk交给writer写入underlying sink
5. 出现以下情况是关闭pipeTo，释放reader、writer
  a. source error时，如果options.preventAbort为false，中断destination
  b. source close时，如果options.preventClose为false，关闭destination
  c. destination error或close时，如果options.preventCancel为false，取消source
  d. abortSignal触发时，中断destination，取消source
```

### pipeThrough(transform, options)

写入转换流

```
1. source、transform.writable锁定时，抛出TypeError
2. source pipeTo transform.writable
3. 返回transform.readable
```

### tee()

流分支

```

```


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

* type：数据源类型
 * bytes：字节类型
* autoAllocateChunkSize：字节类型数据源的chunk大小

### 回调方法

* start(controller)：开始回调，可读流创建时调用
 * push source时负责将数据传递给queue
 * pull source时负责与数据源连接
* pull(controller): 读取回调，可读流读取chunk时调用
 * push source时负责唤醒推送
 * pull source时负责从数据源中读取数据
* cancel(controller): 取消回调，可读流取消时调用，负责通知数据源关闭

### 推送数据

```javascript
function makeReadableWebSocketStream (url, protocols) {
  const ws = new WebSocket(url, protocols)
  ws.binaryType = "arraybuffer"

  return new Readable({
    start (controller) {
      ws.onmessage = event => controller.enqueue(event.data)
      ws.onclose = () => controller.close()
      ws.onerror = () => controller.error(new Error("The WebSocket errored!"))
    }

    cancel () {
      ws.close()
    }
  })
}
```

### 拉取数据

```javascript
const fs = require('fs').promises
const CHUNK_SIZE = 1024

function makeReadableFileStream (filename) {
  let fileHandle
  let position = 0

  return new ReadableStream({
    async start () {
      fileHandle = await fs.open(filename, 'r')
    },

    async pull (controller) {
      const buffer = new ArrayBuffer(CHUNK_SIZE)

      const { bytesRead } = await fileHandle.read(buffer, 0, CHUNK_SIZE, position)

      if (bytesRead === 0) {
        await fileHandle.close()
        controller.close()
      } else {
        position += bytesRead
        controller.enqueue(new Unit8Array(buffer, 0, bytesRead))
      }
    },

    cancel () {
      return fileHandle.close()
    }
  })
}
```

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
| [[queueTotalSize]] | Number | 内部队列中所有chunk的尺寸 |
| [[strategySizeAlgorithm]] | (chunk) -> Number | 计算chunk的尺寸 |
| [[strategyHWM]] | Number | high water mark |
| [[started]] | Boolean | underlyingSource是否已执行完start回调 |
| [[pulling]] | Boolean | 是否正在读取chunk |
| [[pullAgain]] | Boolean | 是否继续读取下一个chunk |
| [[closeRequested]] | Boolean | 是否已结束读取数据 |
| [[pullAlgorithm]] | () -> Promise<void> | 读取chunk |
| [[cancelAlgorithm]] | (reason) => Promise<void> | 取消读取 |

queue entry结构

* value：chunk值
* size：chunk大小

### 抽象操作

#### ReadableStreamDefaultControllerShouldCallPull(controller)

是否可以读取chunk

```
1. stream.[[state]]为readable
2. controller.[[started]]为true，[[closeRequested]]为false，
3. reader有read request或controller的desiredSize大于0
```

#### ReadableStreamDefaultControllerCallPullIfNeeded(controller)

读取chunk

```
1. 不可以读取chunk时，直接返回
2. controller.[[pulling]]为false时，设置[[pulling]]为true，执行controller的[[pullAlgorithm]]
  a. 成功时，重置controller.[[pulling]]为false
    i. controller.[[pullAgain]]为true时，重置[[pullAgain]]为false并读取下一个chunk
  b. 失败时，controller抛出错误
3. controller.[[pulling]]为true时，设置controller.[[pullAgain]]为true
```

### 特征属性

* desiredSize：内部队列的desired size

### 特征方法

#### enqueue(chunk)

chunk进入队列

```
1. 有read request，将chunk交给read request处理
2. 无read request，计算chunk大小，添加进[[queue]]中
3. 读取下一个chunk
```

#### error(e)

controller抛出错误

```
1. 清空队列，[[queue]]置空，[[queueTotalSize]]置0
2. 清除算法，[[strategySizeAlgorithm]]、[[pullAlgorithm]]、[[cancelAlgorithm]]置为undefined
3. stream抛出错误
```

#### close()

controller关闭

```
1. controller.[[closeRequested]]设为true
2. controller.[[queue]]为空时，清除controller算法，执行stream关闭
```

### 内部方法

#### [[pullSteps]] (readRequest)

default reader从controller中读取chunk

```
1. [[queue]]为空时
  a. read request存储到reader.[[readRequests]]中
  b. controller读取chunk
2. [[queue]]不为空时，从[[queue]]中取出chunk交给read request处理
  a. [[closeRequested]]为true且[[queue]]为空时，清除controller算法，执行stream关闭
  b. [[closeRequested]]为false时，读取下一个chunk
```

#### [[CancelSteps]] (reason)

stream取消时的controller操作

```
1. controller清空队列、清除算法
3. 执行controller的[[cancelAlgorithm]]
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

directionary ReadableStreamDefaultReadResult {
  any value;
  boolean done;
}
```

### 内部属性

| 内部属性 | 类型 | 描述 |
| --- | --- | --- |
| [[ownerReadableStream]] | ReadableStream | 所属的可读流 |
| [[readRequests]] | `list<ReadRequest>` | 读取请求列表 |
| [[closedPromise]] | Promise | 读取器是否结束读取 |

read request结构

* chunk steps：读取步骤
* close steps：关闭步骤
* error steps：错误步骤

### new ReadableStreamDefaultReader(stream)

创建可读流的默认读取器

```
1. stream锁定时，抛出TypeError
2. 创建reader对象，初始化属性
  a. reader.[[ownerReadableStream]]为stream，stream.[[reader]]为reader
  b. reader.[[readRequests]]为空list
  c. reader.[[closedPromse]]根据stream.[[state]]设置
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
| [[queue]] | List<(buffer, byte offset, byte length)> | 内部队列 |
| [[queueTotalSize]] | Number | 内部队列中所有chunk的尺寸 |
| [[autoAllocateChunkSize]] | Number | 每个chunk的尺寸 |
| [[strategyHWM]] | Number | high water mark |
| [[started]] | Boolean | underlyingSource是否已执行完start回调 |
| [[pulling]] | Boolean | 是否正在读取chunk |
| [[pullAgain]] | Boolean | 是否继续读取下一个chunk |
| [[closeRequested]] | Boolean | 是否已结束读取数据 |
| [[pullAlgorithm]] | () -> Promise<void> | 读取chunk |
| [[cancelAlgorithm]] | (reason) => Promise<void> | 取消读取 |
| [[pendingPullIntos]] | List<buffer, byte offset, buffer length, bytes filled, element size, view constructor, reader type> | 正在处理的pull into列表 |
| [[byobRequest]] | ReadablsStreamBYOBRequest | 第一个pull into 对应的byob request |

queue entry结构

* buffer：view的buffer
* byte offset：view的offset
* byte length：view的length

pull into结构

* buffer：view的buffer
* byte offset：view的offset
* byte length：view的length
* bytes filled：view已写入的字节数
* element size：view每个单位的字节数
* view constructor：view构造器
* reader type：读取器类型，default或byob

### 抽象操作

#### ReadableByteStreamControllerShouldCallPull(controller)

是否可以读取chunk

```
1. stream.[[state]]为readable
2. controller.[[started]]为true，[[closeRequested]]为false，
3. reader有read request或read into request或controller的desiredSize大于0
```

#### ReadableByteStreamControllerCallPullIfNeeded(controller)

读取chunk

```
1. 不可以读取chunk时，直接返回
2. controller.[[pulling]]为false时，设置[[pulling]]为true，执行controller的[[pullAlgorithm]]
  a. 成功时，重置controller.[[pulling]]为false
    i. controller.[[pullAgain]]为true时，重置[[pullAgain]]为false并读取下一个chunk
  b. 失败时，controller抛出错误
3. controller.[[pulling]]为true时，设置controller.[[pullAgain]]为true
```

#### ReadableByteStreamControllerProcessPullIntoDescriptorsUsingQueue(controller)

将queue中的chunk写入pull into中

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

controller抛出错误

```
1. 清空队列，[[queue]]置空，[[queueTotalSize]]置0
2. 清除算法，[[pullAlgorithm]]、[[cancelAlgorithm]]置为undefined
3. 清空pull into，[[pendingPullIntos]]置空，[[byobRequest]]置null
4. stream抛出错误
```

#### close()

byob controller关闭

```
1. controller.[[queue]]不为空时，[[closeRequested]]设置为true，直接返回
2. controller.[[queue]]为空时
  a. 第一个descriptor的bytes filled大于0时，抛出TypeError
  b. 重置controller，执行stream关闭
```

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
