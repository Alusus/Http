# Http module for alusus

## Methods
- Http.createServer(callback: ptr[func (connection: ptr[Http.Connection]): Int], port: ptr[Char]): ptr[Http.Context] <br> ```create server with custom port and invoke callback on any request```
- Http.closeServer(context: ptr[Http.Context]): Void <br> ```stop server```
- Http.print(connection: ptr[Http.Connection], format: ptr[Char], ...any): Int <br> ```write string to connection```

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
        def context: ptr[Http.Context] = Http.createServer(callbackRequest~ptr, "8080"); // Server is running in separate thread, when navigating to http://localhost:8080/ will invoke callbackRequest
        Console.print("it's work in port 8080: http://localhost:8080/\npress enter to close server: ");
        Console.getChar(); // stop proccess here waiting user to press enter
        Http.closeServer(context); // stop the server
    };

    func callbackRequest(connection: ptr[Http.Connection]): Int {
        def requestInfo: ptr[Http.RequestInfo] = Http.getRequestInfo(connection);
        def content: array[Char, 1024];

        String.assign(content~ptr, "<h1>Welcome from alusus</h1><p> you are in \"%s\"", requestInfo~cnt.localUri);
        Http.print(connection, "HTTP/1.1 200 OK\r\n");
        Http.print(connection, "Content-Type: text/html\r\n");
        Http.print(connection, "Content-Length: %d\r\n\r\n", String.getLength(content~ptr));
        Http.print(connection, "%s", content~ptr);

        return 1;
    };
};

TestModule.start();
```