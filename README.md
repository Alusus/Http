# HTTP

[[عربي]](README.ar.md)

A library for creating HTTP servers with WebSocket support using Alusus Language. This library is based on civetweb.

## Adding to the Project

You can add it to the project using APM:

```
import "Apm.alusus";
Apm.importPackage("Alusus/Http@0.3");
```

## Example

```
import "Srl/Console.alusus";
import "Srl/String.alusus";
import "Srl/Memory.alusus";
import "Apm.alusus";
Apm.importPackage("Alusus/Http@0.3");

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

```
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

#### stopFlag

```
def stopFlag: int
```
Whether to stop the event loop.

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
Each callback is a pointer to a function that the server executes.

#### beginRequest

```
def beginRequest: RequestCallback
```
The server calls it when starting a request.

* `0`: if the request was not processed.
* `1` ~ `99`: server let the function process the request.

#### endRequest

```
def endRequest: ptr[func (connection: ptr[Connection], replyStatusCode: Int)]
```
Called by the server when a request on a given connection ends.

#### logMessage

```
def logMessage: ptr[func (connection: ptr[Connection], message: CharsPtr): Int]
```
Used to log a message on a given connection.

* `0`: The server should call the default routine to log the message.
* Non-zero value: Message logging is done by the callback and default logger shouldn't be called.

#### logAccess

```
def logAccess: ptr[func (connection: ptr[Connection], message: CharsPtr): Int]
```
Used to log an access message on a given connection.

* `0`: The server should call the default logger.
* Non-zero value: The server should not call the default logger.

#### initSsl

```
def initSsl: ptr[func (sslContext: ptr[Void], userData: ptr[Void]): Int]
```
Called when initializing SSL.

* `0`: Server should configure SSL certificate.
* `1`: SSL certificate is configured and the server should do nothing.
* `-1`: SSL initialization failed.

#### connectionClose

```
def connectionClose: ptr[func (connection: ptr[Connection]): Void]
```
Called when a given connection is closed.

#### httpError

```
def httpError: ptr[func (connection: ptr[Connection], status: Int, msg: ptr[array[Char]]): Int]
```
Called when an error occurs on a given connection with the status and a message.

* `0` when the function sends the error page.
* Non-zero value when the function does not send the error page and server should do that instead.

#### initContext

```
def initContext: ptr[func (context: ptr[Context]): Void]
```
Called to initialize the context of a given thread.

#### exitContext

```
def exitContext: ptr[func (context: ptr[Context]): Void]
```
Called to exit the context.

#### initThread

```
def initThread: ptr[func (context: ptr[Context], threadType: Int): Void]
```
Called to initialize a thread with a given context, for a given type.

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

#### requestMethod

```
def requestMethod: CharsPtr
```
Request type, for example: GET.

#### requestUri

```
def requestUri: CharsPtr
```
The URI of the request.

#### localUri

```
def localUri: CharsPtr
```
The local URI of the request, used for requests on the same server.

#### httpVersion

```
def httpVersion: CharsPtr
```
HTTP protocol version used in the request.

#### queryString

```
def queryString: CharsPtr
```
Request parameters in the request.

#### remoteUser

```
def remoteUser: CharsPtr
```
The remote user we deal with.

#### remoteAddr

```
def remoteAddr: array[Char, 48]
```
IP address of the remote user.

#### contentLength

```
def contentLength: Int[64]
```
Request body length in bytes, -1 if nothing specified.

#### remotePort

```
def remotePort: Int
```
The port used on the remote user machine.

#### isSsl

```
def isSsl: Int
```
Whether the connection uses SSL.

#### userData

```
def userData: ptr[Void]
```
Custom user data that is passed to `startServer`.

#### connData

```
def connData: ptr[Void]
```
Data specific to the connection.

#### numberHeaders

```
def numberHeaders: Int
```
Request headers number.

#### httpHeaders

```
def httpHeaders: array[Header, 64]
```
Request headers.

### Header

```
class Header {
    def name: CharsPtr;
    def value: CharsPtr;
};
```
This class holds the information about request header.

#### name

```
def name: CharsPtr
```
Header name, which is the key.

#### value

```
def value: CharsPtr
```
Header value under the key `name`.

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

#### requestInfo

```
def requestInfo: ptr[RequestInfo]
```
Information about the request sent with the connection.

#### context

```
def context: ptr[Context]
```
Information about connection context.

#### ssl

```
def ssl: ptr
```
SSL descriptor.

#### clientSslContext

```
def clientSslContext: ptr
```
Client context on SSL.

#### client

```
def client: ptr
```
The client connected to this connection.

#### connectionBirthTime

```
def connectionBirthTime: Int
```
The time of creating the connection (wall time).

#### requestTime

```
def requestTime: Int
```
The time of creating the connection (server time).

#### numberBytesSent

```
def numberBytesSent: int[64]
```
Number of bytes sent to the client.

#### contentLen

```
def contentLen: int[64]
```
The value of "Content-length" header.

#### consumedContent

```
def consumedContent: int[64]
```
Number of bytes read from content.

#### isChunked

```
def isChunked: int
```
Is data transfering chuncked? Takes one of those values:

* `0`: Transfer is not chuncked.
* `1`: Transfer is not chuncked, and there is still some data to read.
* `2`: Transfer is not chuncked, and no data left to read.

#### chunkRemainder

```
def chunkRemainder: word[64]
```
Data not have been read yet from the last chunck.

#### buf

```
def buf: CharsPtr
```
Buffer for recieved data.

#### pathInfo

```
def pathInfo: CharsPtr
```
Path info part of the URI.

#### mustClose

```
def mustClose: int
```
Should we close the connection?

#### inErrorHandler

```
def inErrorHandler: int
```
Whether the errors are being handled.

#### internalError

```
def internalError: int
```
Whether an error occured while processing the request.

#### bufSize

```
def bufSize: int
```
Buffer size.

#### requestLen

```
def requestLen: int
```
Total size in bytes of the request and the headers in the buffer.

#### dataLen

```
def dataLen: int
```
Total size in bytes of the data in the buffer.

#### statusCode

```
def statusCode: int
```
Reply status code of the HTTP protocol.

#### throttle

```
def throttle: int
```
Throttle value.

#### lastThrottleTime

```
def lastThrottleTime: int
```
Last time throttled data was sent.

#### lastThrottleBytes

```
def lastThrottleBytes: int[64]
```
The bytes recieved in this second.

#### mutex

```
def mutex: int[64]
```
Used for locking when we need to access synced data safely.

### startServer

```
@expname[mg_start]
func startServer(callbacks: ptr[Callbacks], userData: ptr, options: ptr[CharsPtr]): ptr[Context]
func startServer(callback: RequestCallback, userData: ptr, options: ref[Srl.Array[CharsPtr]]): ptr[Context]
func startServer(callback: RequestCallback, options: ref[Srl.Array[CharsPtr]]): ptr[Context]
func startServer(callback: RequestCallback, userData: ptr, optsCount: Int, opts: ...CharsPtr): ptr[Context]
func startServer(callback: RequestCallback, optsCount: Int, opts: ...CharsPtr): ptr[Context]
func startServer(callback: RequestCallback, userData: ptr, port: CharsPtr): ptr[Context]
func startServer(callback: RequestCallback, port: CharsPtr): ptr[Context]
```
Initialize and start the server. Returns a pointer to the server's context, or null in case of initialization failure. In case a callback is not present in the list, the server will use a default one.

* `callbacks`: The list of callbacks used to determine the behaviour of the server and how it processes requests.
* `callback`: Callback that the server will execute when receiving a request.
* `userData`: Optional custom user data passed to `startServer`.
* `options`: Options related to server initialization, like port number. Can be a raw pointer or an array.
* `optsCount`: Number of options in `opts`.
* `opts`: Options related to server initialization, like port number.
* `port`: Port number that the server listens to.

### stopServer

```
@expname[mg_stop]
func stopServer(context: ptr[Context]): Void
```
Close the server and release any acquired resources. Waits until all threads have finished, then releases resources and closes the server.

* `context`: Pointer to the server's context to close.

### read

```
@expname[mg_read]
func read(connection: ptr[Connection], buffer: ptr, bufferSize: Int): Int
```
Read data from the connection in binary format and store it in `buffer`. Returns the number of bytes read on success, `0` when the connection is closed by a peer, or a negative value when there is no more data to read.

* `connection`: Connection to read data from.
* `buffer`: The buffer to store data in.
* `bufferSize`: Max size in bytes of data we can store in `buffer`.

### write

```
@expname[mg_write]
func write(connection: ptr[Connection], buffer: CharsPtr, bufferSize: Int): Int
```
Send data through a given connection. Returns the number of sent bytes on success, or `-1` on failure.

* `connection`: Connection to send through.
* `buffer`: Buffer holding the data to send.
* `bufferSize`: The size in bytes of `buffer`.

### print

```
@expname[mg_printf]
func print(connection: ptr[Connection], format: CharsPtr, ...any): Int
```
Send formatted messages through a given connection. Returns the number of bytes sent on success, `0` when the connection is closed, or `-1` on error.

* `connection`: Connection to send through.
* `format`: Message's format.
* `...any`: Arguments needed to fill `format`.

### sendFile

```
@expname[mg_send_file]
func sendFile(connection: ptr[Connection], fileName: CharsPtr): Void
```
Send a file through a connection. Adds the required headers automatically.

* `connection`: The connection to send through.
* `fileName`: The name of the file to send.

### getCookie

```
@expname[mg_get_cookie]
func getCookie(cookiesString: CharsPtr, cookieName: CharsPtr, outCookieContent: CharsPtr, outCookieSize: Word[64]): Int
```
Get the value of a specific variable from a specific cookie. Returns the size of the cookie in bytes on success, `-1` when the cookie is not found, or `-2` on failure to store in the buffer.

* `cookiesString`: Cookie name.
* `cookieName`: Name of the variable in `cookiesString`.
* `outCookieContent`: Buffer to store the content of the variable in.
* `outCookieSize`: The size in bytes of `outCookieContent`.

### getHeader

```
@expname[mg_get_header]
func getHeader(connection: ptr[Connection], headerName: CharsPtr): CharsPtr
```
Get a header from a given connection. Returns a pointer to the header value or null on failure.

* `connection`: The connection to get a header from.
* `headerName`: The name of the header to retrieve.

### getRequestInfo

```
@expname[mg_get_request_info]
func getRequestInfo(connection: ptr[Connection]): ptr[RequestInfo]
```
Get the information about a request through a given connection. Returns a pointer to request information.

* `connection`: The connection to get request information from.

### getVariable

```
@expname[mg_get_var]
func getVariable(data: CharsPtr, dataSize: Int, variableName: CharsPtr, outVariable: CharsPtr, outVariableSize: Int): Int
```
Get the value of a given variable passed to the server through POST (body) or GET (URI). Returns the size in bytes of the variable value on success, `-1` when the variable is not found, or `-2` on failure to store in the buffer.

* `data`: The data the variable was passed through (POST body or GET URI).
* `dataSize`: Size in bytes of `data`.
* `variableName`: The name of the variable to retrieve.
* `outVariable`: Output buffer to store the value of the variable.
* `outVariableSize`: The size in bytes of `outVariable`.

## License

Copyright (C) 2026 Sarmad Abdullah

This project is licensed under the GNU Lesser General Public License v3.0 (LGPL-3.0). See the `COPYING` and `COPYING.LESSER` files for details.
