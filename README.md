UnitySocketIO_WebSocketSharp
============================

[UnitySocketIO](https://github.com/NetEase/UnitySocketIO) using [websocket-sharp](https://github.com/sta/websocket-sharp) instead of [WebSocket4Net](https://github.com/kerryjiang/WebSocket4Net)

- UnitySocketIO is the only usable socket.io client for unity3d, but it's using WebSocket4Net and SuperSocket.ClientEngine library, which are somewhat buggy and unstable.
- There are times when the MessageReceived event handler is not called, even though the client has received a message packet from the server, and the message silently gets dropped.
- When WebSocket4Net library used by UnitySocketIO was replaced with websocket-sharp, the problem has been solved.
