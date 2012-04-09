# Comet 套接字

## 使用分块数据响应(chunked responses)创建 Comet 套接字

**块响应(Chunked responses)**的一个很有用的特点就是创建Comet套接字. Comet 套接字其实是一种仅仅包含`<script>`标签的数据块形式的`text/html` 响应. 对于每一个数据块，我们都包含一个 `<script>` 标签，让客户端浏览器可以直接执行. 这样我们就可以从服务器端实时的给浏览器传输事件信息: 每一条信息都可以调用一个客户端回调函数，并且包含在 `<script>` 标签内，然后写入响应数据块中.
    
让我们来先写一个实验性质的框架: 创建一个包含 `<script>` 标签的响应块，并且调用浏览器的 `console.log` 函数:
    
```
public static Result index() {
  // Prepare a chunked text stream
  Chunks<String> chunks = new StringChunks() {
    
    // Called when the stream is ready
    public void onReady(Chunks.Out<String> out) {
      out.write("<script>console.log('kiki')</script>");
      out.write("<script>console.log('foo')</script>");
      out.write("<script>console.log('bar')</script>");
      out.close();
    }
    
  }
  
  response().setContentType("text/html");
  
  ok(chunks);
}
```

如果你通过浏览器调用这个函数，你就会从浏览器的console看到三条输出记录.

## 使用 `play.libs.Comet` 助手

我们提供一个 Comet 助手来处理跟上面的实例基本相似的场景，它的实现也基本与上面相同.

> **注意:** 实际上这个助手做的工作更多一点, 比如为了保证浏览器兼容性会先推送一个空的数据块过去, 同时支持JSON和XML等.

现在我们重新来写上面的示例:

```
public static Result index() {
  Comet comet = new Comet("console.log") {
    public void onConnected() {
      sendMessage("kiki");
      sendMessage("foo");
      sendMessage("bar");
      close();
    }
  };
  
  ok(comet);
}
```

## iframe 技术

编写一个Comet套接字的标准方式是，在iframe中读取响应数据块，并且调用一个父框架(parent frame)里的回调函数来:

```
public static Result index() {
  Comet comet = new Comet("parent.cometMessage") {
    public void onConnected() {
      sendMessage("kiki");
      sendMessage("foo");
      sendMessage("bar");
      close();
    }
  };
  
  ok(comet);
}
```

HTML页面是这样:

```
<script type="text/javascript">
  var cometMessage = function(event) {
    console.log('Received event: ' + event)
  }
</script>

<iframe src="/comet"></iframe>
```

> **Next:** [[Web套接字WebSockets | JavaWebSockets]]