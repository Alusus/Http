# WebSocket Support Documentation

## Overview

The Alusus HTTP module now includes comprehensive WebSocket support, built on top of the CivetWeb library. WebSockets provide full-duplex communication channels over a single TCP connection, enabling real-time bidirectional communication between clients and servers.

## Features

- **Real-time Communication**: Bidirectional messaging between client and server
- **Event-driven Architecture**: Callback-based handling for connection events
- **Text and Binary Messages**: Support for both text and binary data transmission
- **Connection Management**: Automatic handling of WebSocket handshake and connection lifecycle
- **Integration**: Seamless integration with existing HTTP server functionality

## WebSocket Callback Types

### WebSocketConnectCallback
```alusus
def WebSocketConnectCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Int];
```
Called when a new WebSocket connection is established. Return 0 to accept the connection, non-zero to reject.

### WebSocketReadyCallback
```alusus
def WebSocketReadyCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```
Called when the WebSocket connection is ready for communication.

### WebSocketDataCallback
```alusus
def WebSocketDataCallback: alias ptr[func (connection: ptr[Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int];
```
Called when data is received from the client. The `bits` parameter indicates the frame type:
- `1`: Text frame
- `2`: Binary frame
- `8`: Close frame
- `9`: Ping frame
- `10`: Pong frame

### WebSocketCloseCallback
```alusus
def WebSocketCloseCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```
Called when a WebSocket connection is closed.

## Core Functions

### setWebSocketHandler
```alusus
func setWebSocketHandler(
    context: ptr[Context],
    uri: CharsPtr,
    connectHandler: WebSocketConnectCallback,
    readyHandler: WebSocketReadyCallback,
    dataHandler: WebSocketDataCallback,
    closeHandler: WebSocketCloseCallback,
    userData: ptr[Void]
): Void;
```
Registers WebSocket handlers for a specific URI path.

**Parameters:**
- `context`: Server context returned by `startServer()`
- `uri`: URI path for WebSocket endpoint (e.g., "/websocket")
- `connectHandler`: Callback for new connections
- `readyHandler`: Callback when connection is ready
- `dataHandler`: Callback for incoming messages
- `closeHandler`: Callback when connection closes
- `userData`: Optional user data passed to callbacks

### setWebSocketHandlerWithSubprotocols
```alusus
func setWebSocketHandlerWithSubprotocols(
    context: ptr[Context],
    uri: CharsPtr,
    subprotocols: ptr[CharsPtr],
    connectHandler: WebSocketConnectCallback,
    readyHandler: WebSocketReadyCallback,
    dataHandler: WebSocketDataCallback,
    closeHandler: WebSocketCloseCallback,
    userData: ptr[Void]
): Void;
```
Same as `setWebSocketHandler` but with support for WebSocket subprotocols.

## Message Sending Functions

### writeWebSocket
```alusus
func writeWebSocket(connection: ptr[Connection], opcode: Int, data: CharsPtr, dataLen: Int): Int;
```
Send raw WebSocket frame with specified opcode.

**Opcodes:**
- `1`: Text frame
- `2`: Binary frame
- `8`: Close frame
- `9`: Ping frame
- `10`: Pong frame

### writeWebSocketText
```alusus
func writeWebSocketText(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```
Send text message to WebSocket client.

### writeWebSocketBinary
```alusus
func writeWebSocketBinary(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```
Send binary message to WebSocket client.

### closeWebSocket
```alusus
func closeWebSocket(connection: ptr[Connection]): Int;
```
Close WebSocket connection gracefully.

## Usage Example

```alusus
import "Srl/Console.alusus";
import "Srl/String.alusus";
import "Apm.alusus";
Apm.importFile("Alusus/Http");

module WebSocketExample {
    use Srl;

    func start() {
        // Start HTTP server
        def context: ptr[Http.Context] = Http.startServer(handleHttpRequest~ptr, "8080");
        
        // Register WebSocket handler
        Http.setWebSocketHandler(
            context,
            "/websocket",
            onConnect~ptr,
            onReady~ptr,
            onData~ptr,
            onClose~ptr
        );

        Console.print("Server listening on port 8080\n");
        Console.getChar();
        Http.stopServer(context);
    };

    func handleHttpRequest(connection: ptr[Http.Connection]): Int {
        // Handle regular HTTP requests
        Http.print(connection, "HTTP/1.1 200 OK\r\n");
        Http.print(connection, "Content-Type: text/html\r\n\r\n");
        Http.print(connection, "<h1>WebSocket Server</h1>");
        return 1;
    };

    func onConnect(connection: ptr[Http.Connection], userData: ptr[Void]): Int {
        Console.print("WebSocket client connected\n");
        return 0; // Accept connection
    };

    func onReady(connection: ptr[Http.Connection], userData: ptr[Void]): Void {
        Console.print("WebSocket ready\n");
        Http.writeWebSocketText(connection, "Welcome!", 8);
    };

    func onData(connection: ptr[Http.Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int {
        if bits == 1 { // Text frame
            Console.print("Received: ");
            Console.print(data, dataLen);
            Console.print("\n");
            
            // Echo message back
            Http.writeWebSocketText(connection, data, dataLen);
        }
        return 1;
    };

    func onClose(connection: ptr[Http.Connection], userData: ptr[Void]): Void {
        Console.print("WebSocket client disconnected\n");
    };
};

WebSocketExample.start();
```

## Client-Side JavaScript Example

```javascript
const ws = new WebSocket('ws://localhost:8080/websocket');

ws.onopen = function(event) {
    console.log('Connected to WebSocket server');
    ws.send('Hello Server!');
};

ws.onmessage = function(event) {
    console.log('Received:', event.data);
};

ws.onclose = function(event) {
    console.log('Connection closed');
};

ws.onerror = function(error) {
    console.log('WebSocket error:', error);
};
```

## Best Practices

1. **Error Handling**: Always check return values from WebSocket functions
2. **Resource Management**: Properly handle connection cleanup in close callbacks
3. **Message Validation**: Validate incoming message data before processing
4. **Connection Limits**: Consider implementing connection limits for production use
5. **Security**: Implement proper authentication and authorization for WebSocket endpoints

## Troubleshooting

### Common Issues

1. **Connection Refused**: Ensure the server is running and the port is accessible
2. **Handshake Failed**: Check that the WebSocket endpoint URI is correctly registered
3. **Message Not Received**: Verify that the data callback is properly handling the message type
4. **Connection Drops**: Implement ping/pong handling for connection keep-alive

### Debugging Tips

- Use browser developer tools to inspect WebSocket frames
- Add logging in callback functions to trace connection lifecycle
- Check server logs for handshake and connection errors
- Verify that the CivetWeb library has WebSocket support compiled in