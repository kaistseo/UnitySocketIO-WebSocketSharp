UnitySocketIO-WebSocketSharp
============================

[UnitySocketIO](https://github.com/NetEase/UnitySocketIO) using [websocket-sharp](https://github.com/sta/websocket-sharp) instead of [WebSocket4Net](https://github.com/kerryjiang/WebSocket4Net)

- UnitySocketIO is the only usable socket.io client for unity3d, but it's using WebSocket4Net and SuperSocket.ClientEngine as underlying libraries, which are somewhat buggy and unstable.
- There are times when the MessageReceived event handler is not called, even though the client has received a message packet from the server, and the message silently gets dropped.
- When WebSocket4Net library used by UnitySocketIO was replaced with websocket-sharp, the problem has been solved.
- [SimpleJson](https://github.com/facebook-csharp-sdk/simple-json) library used by UnitySocketIO was updated to the latest version (v0.30.0), because the old one caused crashes in some cases.

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
  ...
```

### Building for iOS devices

If you want to build this library for iOS devices, you have to copy all the sources of [SimpleJson](https://github.com/facebook-csharp-sdk/simple-json), [websocket-sharp](https://github.com/sta/websocket-sharp), [UnitySocketIO](https://github.com/NetEase/UnitySocketIO) to a folder of your project, instead of the DLLs.

- And before you build it, you have to modify WebSocket.cs of websocket-sharp as follows:

```
internal delegate bool SendBytesFunc (Opcode opcode, byte [] data);

private void send (Opcode opcode, byte [] data, Action<bool> completed)
{
  //Func<Opcode, byte [], bool> sender = send;
  SendBytesFunc sender = send;
  ...
}
```  

- Modify Net/ListenerAsyncResult.cs of websocket-sharp as follows:

```
internal void Complete (Exception exc)
{
  ...
  //ThreadPool.UnsafeQueueUserWorkItem (InvokeCB, this);
  ThreadPool.QueueUserWorkItem (InvokeCB, this);
}

...

internal void Complete (HttpListenerContext context, bool synch)
{
  ...
  //ThreadPool.UnsafeQueueUserWorkItem (InvokeCB, this);
  ThreadPool.QueueUserWorkItem (InvokeCB, this);
}
```

- Uncomment "#define SIMPLE_JSON_NO_LINQ_EXPRESSION" in SimpleJson.cs.
