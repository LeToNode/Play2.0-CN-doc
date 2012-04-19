# HTTP 响应数据流

## 标准响应和Content-Length头信息

自从HTTP1.1协议开始，为了让一个HTTP连接能够响应多个请求，服务器必须向客户端响应明确的'Content-Length'头信息。

默认情况下，当你发起一个简单的HTTP请求，如:

```
public static Result index() {
  return ok("Hello World")
}
```

你并没有指定一个'Content-Length'头信息.很显然这是因为你要发送的数据很明确，Play可以计算出它的长度帮你生成头信息(Header).

> **注意** 这种文本类型的内容并非看上去那么简单，因为'Content-Length'的头信息必须通过所使用编码的字节符号来计算.

为了计算 `Content-Length` , Play 必须处理整个response数据，并且把这些数据读取到内存中. 

## 处理文件

如果说把一些简单的内容加载到内存中问题不大，那么加载一个很大的数据集呢？比如我们需要向客户端发送一个很大的文件.

Play 提供了很简单的助手类帮助你做这种事情:

```
public static Result index() {
  return ok(new java.io.File("/tmp/fileToServe.pdf"));
}
```

并且程序会通过文件来计算 `Content-Type` . 并且额外的加上 `Content-Disposition` 头信息，用来告知客户端如何去处理这个请求响应. 默认情况下浏览器会通过`Content-Disposition: attachment; filename=fileToServe.pdf`来下载文件内容.

## 分块响应(Chunked Response)

由于我们可以计算出文件的流数据大小，所以目前位置处理文件进行的很好. 但是如果我们要处理一些没有文件长度的，动态的数据流呢?

为了解决此类问题，我们需要使用 **Chunked transfer encoding(块传输编码)**. 

> **Chunked transfer encoding(块传输编码)** 是 HTTP 1.1 协议中的一种让服务器通过块来处理数据的传输机制. 它使用`Transfer-Encoding` HTTP头信息来代替 `Content-Length` 头. 由于 `Content-Length` 不再使用, 服务器就不需要在传输数据之前就获取数据长度了.
> 
> 每一个chunk(块)在传输之前都会把自己的长度告诉客户端，这样客户端就可以知道什么时候结束接收数据.数据传输会在最后一个块的长度为0的时候结束.
>
> [[http://en.wikipedia.org/wiki/Chunked_transfer_encoding]]

这种特点的优势就是 **实时**, 就是说我们可以在数据可用的时候马上传输数据.而缺点就是，由于客户端不知道总得内容长度，所以无法提供一个下载进度条.

我们假设有一个服务提供一个动态的'InputStream'数据流，我们可以直接通过Play使用一下块传输：

```
public static Result index() {
  InputStream is = getDynamicStreamSomewhere();
  return ok(is);
}
```

你也可以创建自己的块响应器. Play API支持文本和二进制的块传输响应 (via `String` and `byte[]`):

```
public static index() {
  // Prepare a chunked text stream
  Chunks<String> chunks = new StringChunks() {
    
    // Called when the stream is ready
    public void onReady(Chunks.Out<String> out) {
      registerOutChannelSomewhere(out);
    }
    
  }
  
  // Serves this stream with 200 OK
  ok(chunks);
}
```
 `onReady` 函数会在当前输出流安全的情况下开始执行，它给你提供了一个 `Chunks.Out` 通道用来进行数据写入.

假设我们有一个异步的进程(asynchronous process (类似于 `Actor`) ) 给这个流推数据:

```
public void registerOutChannelSomewhere(Chunks.Out<String> out) {
  out.write("kiki");
  out.write("foo");
  out.write("bar");
  out.close();
}
```

我们可以检测服务器发送给客户端的数据:

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Transfer-Encoding: chunked

4
kiki
3
foo
3
bar
0

```

我们获得了三个有内容的块和一个关闭传输的0数据块.

> **Next:** [[Comet 套接字 | JavaComet]]