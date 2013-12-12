UnitySocketIO-WebSocketSharp
============================

[UnitySocketIO](https://github.com/NetEase/UnitySocketIO) using [websocket-sharp](https://github.com/sta/websocket-sharp) instead of [WebSocket4Net](https://github.com/kerryjiang/WebSocket4Net)

- UnitySocketIO is the only usable socket.io client for unity3d, but it's using WebSocket4Net and SuperSocket.ClientEngine library, which are somewhat buggy and unstable.
- There are times when the MessageReceived event handler is not called, even though the client has received a message packet from the server, and the message silently gets dropped.
- When WebSocket4Net library used by UnitySocketIO was replaced with websocket-sharp, the problem has been solved.

###How to use###

Copy all the DLLs from /SocketIO/bin/Debug/ to any folder of your project (e.g. Assets/Plugins/SocketIO/)

###Avoiding too long wait on Close event###

websocket-sharp uses AutoResetEvent.WaitOne on Close event, but it looks like socket.io server doesn't return Close frame and the client must wait until the timeout (5 seconds) expires.

If you add just one line below in the method "private bool close (byte [] frameAsBytes, int timeOut, Func<bool> release)" of "websocket-sharp/WebSocket.cs" file, it'll be solved.

```
private bool close (byte [] frameAsBytes, int timeOut, Func<bool> release)
{
  timeOut = 0; // no closing handshake
  
  var sent = frameAsBytes != null && _stream.Write (frameAsBytes);
```
