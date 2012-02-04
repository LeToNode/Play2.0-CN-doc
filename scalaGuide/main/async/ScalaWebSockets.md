# WebSockets

## Using WebSockets instead of Comet sockets

A Comet socket is a kind of hack allowing to send live events to the web browser. Also it supports only one way communication from the server to the client. Top push events to the server the Web browser can make Ajax requests.

> **Note:** It is also possible to achieve the same kind of live communication the other way around by using an infinite HTTP request handled by a custom `BodyParser` receiving chunks of input data but it is way more complicated.

Modern Web browser support natively two way live communication via WebSockets.

>WebSocket is a web technology providing for bi-directional, full-duplex communications channels, over a single Transmission Control Protocol (TCP) socket. The WebSocket API is being standardized by the W3C, and the WebSocket protocol has been standardized by the IETF as RFC 6455.
>
>WebSocket is designed to be implemented in web browsers and web servers, but it can be used by any client or server application. Because ordinary TCP connections to port numbers other than 80 are frequently blocked by administrators outside of home environments, it can be used as a way to circumvent these restrictions and provide similar functionality with some additional protocol overhead while multiplexing several WebSocket services over a single TCP port. Additionally, it serves a purpose for web applications that require real-time bi-directional communication. Before the implementation of WebSocket, such bi-directional communication was only possible using comet channels; however, a comet is not trivial to implement reliably, and due to the TCP Handshake and HTTP header overhead, it may be inefficient for small messages. The WebSocket protocol aims to solve these problems without compromising security assumptions of the web.
>
> [[http://en.wikipedia.org/wiki/WebSocket]]

## Handling WebSockets

Until now we were using `Action` to handle standard HTTP requests and sending back standard HTTP response. But WebSockets are a totally different beast and can't be handled via standard Action.

So to handle a WebSocket use a `WebSocket` instead of an `Action`:

```
def index = WebSocket.using[String] { request => 
  
  // Log events to the console
  val in = Iteratee.foreach[String](println).mapDone { _ =>
    println("Disconnected")
  }
  
  // Send a single 'Hello!' message
  val out = Enumerator("Hello!")
  
  (in, out)
}
```

A WebSocket has access to the request headers (from the HTTP request which initiate the WebSocket connection) allowing you to retrieve standard headers and session data. But it doesn't have access to any request body, nor to the HTTP response.

When constructing a `WebSocket` this way we must return both `in` and `out` channel. 

- The `in` channel is an `Iteratee[A,Unit]` (where `A` is the message type, here we are using `String`) that will be notified for each message, and will received `EOF` when the socket is closed on the client side.
- The `out` channel is en `Enumerator[A]` that will generate the messages to be sent to the Web client. It can close the connection on the server side by sending `EOF`.

It this example we are creating an simple iteratee that print each message to console. To send messages we create a simple dummy enumerator that will send a single **Hello!** message.

> **Tip:** You can test websocket on [[http://websocket.org/echo.html]]. Just set `ws://localhost:9000` as location.

Let's write another example that discard totally the input data adn that close the socket just after sending the **Hello!** message:

```
def index = WebSocket.using[String] { request => 
  
  // Just consume and ignore the input
  val in = Iteratee.consume[String]()
  
  // Send a single 'Hello!' message and close
  val out = Enumerator("Hello!") >>> Enumerator.eof
  
  (in, out)
}
```

> **Next:** [[The template engine | ScalaTemplates]]