# Web套接字(WebSockets)

## 使用Web套接字(WebSocket)代替Comet套接字(Comet Socket)

用Comet套接字来实现主动、实时的给浏览器发送数据是一种很不优雅的手段. 另外, Coment只支持服务器端到客户端的单路通讯. 如果要从客户端像服务器端推送事件，浏览器可以使用Ajax请求.

现在，流行的Web浏览器通过WebSocket原生的支持了双向的实时通讯.

>WebSocket是一种通过TCP套接字实现双向(bi-directional), 全双工(full-duplex)的通讯信道.WebSocket正在被W3C标准化，并且WebSocket协议已经被IETF作为标准记录进RFC6455.
>
>WebSocket设计为一个需要服务器端和Web浏览器来实现的协议，并且可以用于任何的客户端和应用. Because ordinary TCP connections to port numbers other than 80 are frequently blocked by administrators outside of home environments, it can be used as a way to circumvent these restrictions and provide similar functionality with some additional protocol overhead while multiplexing several WebSocket services over a single TCP port. Additionally, it serves a purpose for web applications that require real-time bi-directional communication. Before the implementation of WebSocket, such bi-directional communication was only possible using comet channels; however, a comet is not trivial to implement reliably, and due to the TCP Handshake and HTTP header overhead, it may be inefficient for small messages. The WebSocket protocol aims to solve these problems without compromising security assumptions of the web.
>
> [[http://en.wikipedia.org/wiki/WebSocket]]

## Handling WebSockets

Until now we were using a simple action method to handle standard HTTP requests and send back standard HTTP results. WebSockets are a totally different beast, and can’t be handled via standard actions.

To handle a WebSocket your method must return a `WebSocket` instead of a `Result`:

```
public static WebSocket<String> index() {
  return new WebSocket<String>() {
      
    // Called when the Websocket Handshake is done.
    public void onReady(WebSocket.In<String> in, WebSocket.Out<String> out) {
      
      // For each event received on the socket,
      in.onMessage(new Callback<String>() {
         public void invoke(String event) {
             
           // Log events to the console
           println(event);  
             
         } 
      });
      
      // When the socket is closed.
      in.onClose(new Callback0() {
         public void invoke() {
             
           println("Disconnected")
             
         }
      });
      
      // Send a single 'Hello!' message
      out.write("Hello!");
      
    }
    
  }
}
```

A WebSocket has access to the request headers (from the HTTP request that initiates the WebSocket connection) allowing you to retrieve standard headers and session data. But it doesn't have access to any request body, nor to the HTTP response.

When the `WebSocket` is ready, you get both `in` and `out` channels.

It this example, we print each message to console and we send a single **Hello!** message.

> **Tip:** You can test your WebSocket controller on [[http://websocket.org/echo.html]]. Just set the location to `ws://localhost:9000`.

Let’s write another example that totally discards the input data and closes the socket just after sending the **Hello!** message:

```
public static WebSocket<String> index() {
  return new WebSocket<String>() {
      
    public void onReady(WebSocket.In<String> in, WebSocket.Out<String> out) {
      out.write("Hello!");
      out.close()
    }
    
  }
}
```

> **Next:** [[The template engine | JavaTemplates]]