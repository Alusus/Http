# HTTP

[[عربي]](readme.ar.md)

A library for creating HTTP servers with WebSocket support using Alusus Language. This library is based on civetweb.

## Adding to the Project

You can add it to the project using APM:

```
import "Apm.alusus";
Apm.importFile("Alusus/Http");
```

## Example

```
import "Srl/Console.alusus";
import "Srl/String.alusus";
import "Srl/Memory.alusus";
import "Apm.alusus";
Apm.importFile("Alusus/Http");

module TestModule {
    use Srl;

    func start() {
        
        // define a pointer to the context of the server.
        // the server is executing the given function on the port 8080
        def context: ptr[Http.Context] = Http.startServer(callbackRequest~ptr, "8080");

        Console.print("Server is listening on port 8080\nhttp://localhost:8080/\nPress enter to close server.");
        // wait for a key press from the user to stop the server
        Console.getChar();
        // when we reach here the user was pressed a key
        // so we should close the server
        Http.stopServer(context);

        Console.print("Server closed.\nPress enter to exit.");
        Console.getChar();
    };

    // the function we want the server to execute
    func callbackRequest(connection: ptr[Http.Connection]): Int {
        // define a variable for holding connection request information.
        def requestInfo: ptr[Http.RequestInfo] = Http.getRequestInfo(connection);
        // define a variable to store the content we want to show
        def content: array[Char, 1024];

        // store a simple html code in `content`
        String.assign(content~ptr, "<h1>Welcome from Alusus</h1><p> you are in \"%s\"", requestInfo~cnt.localUri);

        // write the data through the given connection
        // this data represent some information about the request in addition to the content.
        Http.print(connection, "HTTP/1.1 200 OK\r\n");
        Http.print(connection, "Content-Type: text/html\r\n");
        Http.print(connection, "Content-Length: %d\r\n\r\n", String.getLength(content~ptr));
        Http.print(connection, content~ptr);

        return 1;
    };
};

TestModule.start();
```

## WebSocket Support

This library now includes comprehensive WebSocket support for real-time bidirectional communication. See the complete [WebSocket Documentation](websocket_documentation.en.md) for detailed information.

### Quick WebSocket Example

```alusus
// Register WebSocket handler
Http.setWebSocketHandler(
    context,
    "/websocket",
    onConnect~ptr,
    onReady~ptr,
    onData~ptr,
    onClose~ptr
);

// Send text message
Http.writeTextToWebSocket(connection, "Hello WebSocket!");
```

For a complete working example, see [websocket_server.alusus](Examples/websocket_server.alusus).

## Functions and Types

### Context

```
class Context{
    def stopFlag: int;
};
```
This class holds the information about the context.

`stopFlag`: Whether to stop the event loop.

### Callbacks

```
class Callbacks{
    def beginRequest: RequestCallback;
    def endRequest: ptr[func (connection: ptr[Connection], replyStatusCode: Int)];
    def logMessage: ptr[func (connection: ptr[Connection], message: CharsPtr): Int];
    def logAccess: ptr[func (connection: ptr[Connection], message: CharsPtr): Int];
    def initSsl: ptr[func (sslContext: ptr[Void], userData: ptr[Void]): Int];
    def connectionClose: ptr[func (connection: ptr[Connection]): Void];
    def httpError: ptr[func (connection: ptr[Connection], status: Int, msg: ptr[array[Char]]): Int];
    def initContext: ptr[func (context: ptr[Context]): Void];
    def exitContext: ptr[func (context: ptr[Context]): Void];
    def initThread: ptr[func (context: ptr[Context], threadType: Int): Void];
};
```

This class holds the main callbacks that can be used with http protocol.
Each callback is a poiner to a function that server execute.

**`beginRequest`**: The server calls it when starting a request.

Return value:

* `0`: if the request was not processed.

* `1` ~ `99`: server let the function process the request

**`endRequest`**: Called by the server when a request on a given connection ends.

**`logMessage`**: Used to log a message on a given connection.

Return value:

* `0`: The server should call the default routine to log the message.

* Non-zero value: Message logging is done by the callback and default logger shouldn't be called.

**`logAccess`**: Used to log an access message on a given connection.

Return value:

* `0`: The server should call the default logger.

* Non-zero value: The server should not call the default logger.

**`initSsl`**: Called when initializing SSL.

Return value:

* `0`: Server should configure SSL certificate.

* `1`: SSL certificate is configured and the server should do nothing.

* `-1`: SSL initialization failed.

**`connectionClose`**: CAlled when a given connection is closed.

**`httpError`**: Called when an error occurs on a given connection with the status and a message.

Return value:

* `0` when the function sends the error page.

* Non-zero value when the function does not send the error page and server should do that instead.

**`initContext`**: Called to initialize the context of a given thread.

**`exitContext`** Called to exit the context.

**`initThread`** Called to intialize a thread with a given context, for a given type.

### RequestInfo

```
class RequestInfo {
    def requestMethod: CharsPtr;
    def requestUri: CharsPtr;
    def localUri: CharsPtr;
    def httpVersion: CharsPtr;
    def queryString: CharsPtr;
    def remoteUser: CharsPtr;
    def remoteAddr: array[Char, 48];
    def contentLength: Int[64];
    def remotePort: Int;
    def isSsl: Int;
    def userData: ptr[Void];
    def connData: ptr[Void];
    def numberHeaders: Int;
    def httpHeaders: array[Header, 64];
};
```
This class holds the information about a request. 

`requestMethod`: Request type, for example: GET.

`requestUri`: The URI of the request.

`localUri`: The local URI of the request, used for requests on the same server.

`httpVersion`: HTTP protocol version used in the request.

`queryString`: Request parameters in the request.

`remoteUser`: The remote user we deal with.

`remoteAddr`: IP address of the remote user.

`contentLength`: Request body length in bytes, -1 if nothing specified.

`remotePort`: The port used on the remote user machine.

`isSsl`: Whether the connection uses SSL.

`userData`: Custom user data that is passed to `startServer`.

`connData`: Data specific to the connection.

`numberHeaders`: Request headers number.

`httpHeaders`: Request headers.

### Header

```
class Header {
    def name: CharsPtr;
    def value: CharsPtr;
};
```

This class holds the information about request header.

`name`: Header name, which is the key.

`value`: Header value under the key `name`.

### Connection

```
class Connection {
    def requestInfo: ptr[RequestInfo];
    def context: ptr[Context];
    def ssl: ptr;
    def clientSslContext: ptr;
    def client: ptr;
    def connectionBirthTime: Int;
    def requestTime: Int;
    def numberBytesSent: int[64];
    def contentLen: int[64];
    def consumedContent: int[64];
    def isChunked: int;
    def chunkRemainder: word[64];
    def buf: CharsPtr;
    def pathInfo: CharsPtr;
    def mustClose: int;
    def inErrorHandler: int;
    def internalError: int;
    def bufSize: int;
    def requestLen: int;
    def dataLen: int;
    def statusCode: int;
    def throttle: int;
    def lastThrottleTime: int;
    def lastThrottleBytes: int[64];
    def mutex: int[64];
};
```

This class holds the information about the connection.

`requestInfo`: Information about the request sent with the connection.

`context`: Information about connection context.

`ssl`: SSL descriptor.

`clientSslContext`: Client context on SSL.

`client`: The client connected to this connection.

`connectionBirthTime`: The time of creating the connection (wall time).

`requestTime`: The time of creating the connection (server time).

`numberBytesSent`: Number of bytes sent to the client.

`contentLen`: The value of "Content-length" header.

`consumedContent`: Number of bytes read from content.

`isChunked`: Is data transfering chuncked? Takes one of those values:

* `0`: Transfer is not chuncked.

* `1`: Transfer is not chuncked, and there is still some data to read.

* `2`: Transfer is not chuncked, and no data left to read.

`chunkRemainder`: Data no have been read yet from the last chunck.

`buf`: Buffer for recieved data.

`pathInfo`: Path info part of the URI.

`mustClose`: Should we close the connection?

`inErrorHandler`: Whether the errors are being handled.

`internalError`: Whether an error occured while processing the request.

`bufSize`: Buffer size.

`requestLen`: Total size in bytes of the request and the headers in the buffer.

`dataLen`: Total size in bytes of the data in the buffer.

`statusCode`: Reply status code of the HTTP protocol.

`throttle`: Throttle value.

`lastThrottleTime`: Last time throttled data was sent.

`lastThrottleBytes`: The bytes recieved in this second.

`mutex`: Used for locking when we need to access synced data safely.

### startServer

```
@expname[mg_start]
func startServer(callbacks: ptr[Callbacks], userData: ptr, options: ptr[CharsPtr]): ptr[Context];
```

This function is used for initializing and starting the server.

The behaviour of the server is controlled by a list of callbacks and a list of options that the user gives.

In case that one of the callbacks is not present in the list, the server will use a default one.

Parameters:

`callbacks`: The list of callback that used to determine the behaviour of the server, and how it processes the requests.

`userData`: The optional custo user data that was passed to `startServer`.

`options`: A list of options used in server initialization.

Return value:

A pointer to server's context, or a null in case of initialization failure.

```
func startServer (callback: RequestCallback, userData: ptr, options: ref[Srl.Array[CharsPtr]]): ptr[Context];
func startServer (callback: RequestCallback, options: ref[Srl.Array[CharsPtr]]): ptr[Context];
```

Parameters:

`callback`: callback that server will execute when receiving a request.

`userData`: The optional custo user data that was passed to `startServer`.

`options`: options related to server initialization, like port number.

```
func startServer (callback: RequestCallback, userData: ptr, optsCount: Int, opts: ...CharsPtr): ptr[Context];
func startServer (callback: RequestCallback, optsCount: Int, opts: ...CharsPtr): ptr[Context];
```

Parameters:

`callback`: Callback that server will execute when receiving a request.

`userData`: The optional custo user data that was passed to `startServer`.

`optsCount`: Number of options in `opts`.

`opts`: Options related to server initialization, like port number.

```
func startServer(callback: RequestCallback, userData: ptr, port: CharsPtr): ptr[Context];
func startServer(callback: RequestCallback, port: CharsPtr): ptr[Context];
```

`callback`: Callback that server will execute when receiving a request.

`userData`: The optional custo user data that was passed to `startServer`.

`port`: Port number that server listens to.

### stopServer

```
@expname[mg_stop]
func stopServer(context: ptr[Context]): Void;
```

This function is used for closing the server and release any accquired resources,
it waits until all threads finished, and only then it release resources and close the server.

Parameters:

`context`: Pointer to server's context that we want to close.

### read

```
@expname[mg_read]
func read(connection: ptr[Connection], buffer: ptr, bufferSize: Int): Int;
```

This function read the data from the connection given by `connection` parameter.
Data is considered in binary format and stored in `buffer`.

Parameters:

`connection`: Connection we want to read data from it.

`buffer`: The buffer to store data in it.

`bufferSize`: Max size in bytes of data we can store in `buffer`.

Return value:

On success: Number of bytes it read.

When connection is closed from a peer: 0.

When there is no more data to read: negative value.

### write

```
@expname[mg_write]
func write(connection: ptr[Connection], buffer: CharsPtr, bufferSize: Int): Int;
```

This function is used to send data through a given connection

Parameters:

`connection`: Connection we want to send through it.

`buffer`: Buffer holding the data we want to send.

`bufferSize`: The size in bytes of `buffer`

Return value:

Number of sent bytes in case of success, or -1 in case of failure.

### print

```
@expname[mg_printf]
func print(connection: ptr[Connection], format: CharsPtr, ...any): Int;
```

This function is used for sending formatted messages through a given connection

Parameters:

`connection`: Connection we want to send through it.

`format`: Message's format.

Var args: Arguments needed to fill `format`.

Return value:

When connection is closed: 0.

When an error occurs: -1.

On success: Number of bytes sent.

### sendFile

```
@expname[mg_send_file]
func sendFile(connection: ptr[Connection], fileName: CharsPtr): Void;
```

This function is used to send a file through a connection.
It adds the required headers automatically.

Parameters:

`connection`: The connection we want to send through it.

`fileName`: The name of the file we want to send.

### getCookie

```
@expname[mg_get_cookie]
func getCookie(
    cookiesString: CharsPtr, cookieName: CharsPtr, outCookieContent: CharsPtr, outCookieSize: Word[64]
): Int;
```

This function is used to get the value of a specific variable from a specific cookie. 

Parameters:

`cookiesString`: Cookie name.

`cookieName`: Name of the variable in `cookiesString`.

`outCookieContent`: Buffer to store the content of the variable in it.

`outCookieSize`: The size in bytes of `outCookieContent`.

Return value:

On success: The size of the cookie in bytes.

When cookie is not found: -1.

On failure to store in the buffer: -2.

### getHeader

```
@expname[mg_get_header]
func getHeader(connection: ptr[Connection], headerName: CharsPtr): CharsPtr;
```

This function is used to get a header from a given connection.

Parameters:

`connection`: The connection we want to get a header from it.

`headerName`: The name of header we want.

Return value:

A pointer to the header value or null in case of failure.

### getRequestInfo

```
@expname[mg_get_request_info]
func getRequestInfo(connection: ptr[Connection]): ptr[RequestInfo];
```

This function is used to get the information about a request through a given connection. 

Parameters:

`connection`: The connection we want to get request information from it.

Return value:

A pointer to request information.

### getVariable

```
@expname[mg_get_var]
func getVariable(
    data: CharsPtr, dataSize: Int, variableName: CharsPtr, outVariable: CharsPtr, outVariableSize: Int
): Int;
```

This function is used to get the value of a given variable from the server.

This variable is passed to the server through POST (in the body), or GET (in the URI) request.

Parameters:

`data`: The data we passed the variable through it (POST body, or GET URI).

`dataSize`: Size in bytes of `data`.

`variableName`: The name of the variable we want.

`outVariable`: Output buffer to store the value of the variable in it.

`outVariableSize`: The size in bytes of `outVariable`.

Return value:

On success: The size in bytes of the variable value.

When variable is not found: -1.

On failure to store in the buffer: -2.

