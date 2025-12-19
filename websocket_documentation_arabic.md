# توثيق دعم WebSocket

## نظرة عامة

تتضمن وحدة HTTP في الأسس الآن دعماً شاملاً لـ WebSocket، مبني على مكتبة CivetWeb. توفر WebSockets قنوات اتصال كاملة الإرسال عبر اتصال TCP واحد، مما يتيح التواصل الثنائي الفوري بين العملاء والخوادم.

## الميزات

- **التواصل الفوري**: تبادل الرسائل الثنائي بين العميل والخادم
- **البنية المعتمدة على الأحداث**: معالجة الأحداث باستخدام دوال الاستدعاء
- **الرسائل النصية والثنائية**: دعم إرسال البيانات النصية والثنائية
- **إدارة الاتصالات**: معالجة تلقائية لمصافحة WebSocket ودورة حياة الاتصال
- **التكامل**: تكامل سلس مع وظائف خادم HTTP الحالية

## أنواع دوال الاستدعاء لـ WebSocket

### WebSocketConnectCallback
```alusus
def WebSocketConnectCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Int];
```
يتم استدعاؤها عند إنشاء اتصال WebSocket جديد. إرجاع 0 لقبول الاتصال، أو رقم غير صفر لرفضه.

### WebSocketReadyCallback
```alusus
def WebSocketReadyCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```
يتم استدعاؤها عندما يصبح اتصال WebSocket جاهزاً للتواصل.

### WebSocketDataCallback
```alusus
def WebSocketDataCallback: alias ptr[func (connection: ptr[Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int];
```
يتم استدعاؤها عند استلام البيانات من العميل. يشير المعامل `bits` إلى نوع الإطار:
- `1`: إطار نصي
- `2`: إطار ثنائي
- `8`: إطار إغلاق
- `9`: إطار Ping
- `10`: إطار Pong

### WebSocketCloseCallback
```alusus
def WebSocketCloseCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```
يتم استدعاؤها عند إغلاق اتصال WebSocket.

## الدوال الأساسية

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
تسجل معالجات WebSocket لمسار URI محدد.

**المعاملات:**
- `context`: سياق الخادم المُرجع من `startServer()`
- `uri`: مسار URI لنقطة WebSocket (مثل "/websocket")
- `connectHandler`: دالة الاستدعاء للاتصالات الجديدة
- `readyHandler`: دالة الاستدعاء عندما يصبح الاتصال جاهزاً
- `dataHandler`: دالة الاستدعاء للرسائل الواردة
- `closeHandler`: دالة الاستدعاء عند إغلاق الاتصال
- `userData`: بيانات المستخدم الاختيارية الممررة إلى دوال الاستدعاء

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
مثل `setWebSocketHandler` لكن مع دعم البروتوكولات الفرعية لـ WebSocket.

## دوال إرسال الرسائل

### writeWebSocket
```alusus
func writeWebSocket(connection: ptr[Connection], opcode: Int, data: CharsPtr, dataLen: Int): Int;
```
إرسال إطار WebSocket خام مع رمز العملية المحدد.

**رموز العمليات:**
- `1`: إطار نصي
- `2`: إطار ثنائي
- `8`: إطار إغلاق
- `9`: إطار Ping
- `10`: إطار Pong

### writeWebSocketText
```alusus
func writeWebSocketText(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```
إرسال رسالة نصية إلى عميل WebSocket.

### writeWebSocketBinary
```alusus
func writeWebSocketBinary(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```
إرسال رسالة ثنائية إلى عميل WebSocket.

### closeWebSocket
```alusus
func closeWebSocket(connection: ptr[Connection]): Int;
```
إغلاق اتصال WebSocket بطريقة لائقة.

## مثال على الاستخدام

```alusus
import "Srl/Console.alusus";
import "Srl/String.alusus";
import "Apm.alusus";
Apm.importFile("Alusus/Http");

module WebSocketExample {
    use Srl;

    func start() {
        // بدء تشغيل خادم HTTP
        def context: ptr[Http.Context] = Http.startServer(handleHttpRequest~ptr, "8080");
        
        // تسجيل معالج WebSocket
        Http.setWebSocketHandler(
            context,
            "/websocket",
            onConnect~ptr,
            onReady~ptr,
            onData~ptr,
            onClose~ptr
        );

        Console.print("الخادم يستمع على المنفذ 8080\n");
        Console.getChar();
        Http.stopServer(context);
    };

    func handleHttpRequest(connection: ptr[Http.Connection]): Int {
        // معالجة طلبات HTTP العادية
        Http.print(connection, "HTTP/1.1 200 OK\r\n");
        Http.print(connection, "Content-Type: text/html\r\n\r\n");
        Http.print(connection, "<h1>خادم WebSocket</h1>");
        return 1;
    };

    func onConnect(connection: ptr[Http.Connection], userData: ptr[Void]): Int {
        Console.print("عميل WebSocket متصل\n");
        return 0; // قبول الاتصال
    };

    func onReady(connection: ptr[Http.Connection], userData: ptr[Void]): Void {
        Console.print("WebSocket جاهز\n");
        Http.writeWebSocketText(connection, "أهلاً وسهلاً!", 19);
    };

    func onData(connection: ptr[Http.Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int {
        if bits == 1 { // إطار نصي
            Console.print("تم الاستلام: ");
            Console.print(data, dataLen);
            Console.print("\n");
            
            // إرجاع الرسالة
            Http.writeWebSocketText(connection, data, dataLen);
        }
        return 1;
    };

    func onClose(connection: ptr[Http.Connection], userData: ptr[Void]): Void {
        Console.print("عميل WebSocket منقطع\n");
    };
};

WebSocketExample.start();
```

## مثال JavaScript للعميل

```javascript
const ws = new WebSocket('ws://localhost:8080/websocket');

ws.onopen = function(event) {
    console.log('متصل بخادم WebSocket');
    ws.send('مرحباً أيها الخادم!');
};

ws.onmessage = function(event) {
    console.log('تم الاستلام:', event.data);
};

ws.onclose = function(event) {
    console.log('تم إغلاق الاتصال');
};

ws.onerror = function(error) {
    console.log('خطأ WebSocket:', error);
};
```

## أفضل الممارسات

1. **معالجة الأخطاء**: تحقق دائماً من القيم المُرجعة من دوال WebSocket
2. **إدارة الموارد**: التعامل الصحيح مع تنظيف الاتصالات في دوال الإغلاق
3. **التحقق من الرسائل**: التحقق من صحة بيانات الرسائل الواردة قبل المعالجة
4. **حدود الاتصال**: النظر في تنفيذ حدود الاتصال للاستخدام في الإنتاج
5. **الأمان**: تنفيذ المصادقة والترخيص المناسب لنقاط WebSocket

## استكشاف الأخطاء وإصلاحها

### المشاكل الشائعة

1. **رفض الاتصال**: تأكد من أن الخادم يعمل والمنفذ قابل للوصول
2. **فشل المصافحة**: تحقق من أن URI لنقطة WebSocket مسجل بشكل صحيح
3. **عدم استلام الرسالة**: تحقق من أن دالة استدعاء البيانات تتعامل بشكل صحيح مع نوع الرسالة
4. **انقطاع الاتصال**: تنفيذ معالجة ping/pong للحفاظ على الاتصال

### نصائح التصحيح

- استخدم أدوات المطور في المتصفح لفحص إطارات WebSocket
- أضف تسجيلاً في دوال الاستدعاء لتتبع دورة حياة الاتصال
- تحقق من سجلات الخادم للأخطاء في المصافحة والاتصال
- تحقق من أن مكتبة CivetWeb تحتوي على دعم WebSocket المُجمع

## الخلاصة

يوفر دعم WebSocket في وحدة HTTP للأسس أدوات قوية لبناء تطبيقات الويب التفاعلية والفورية. مع واجهة برمجة التطبيقات البسيطة والمرنة، يمكن للمطورين بسهولة إضافة وظائف التواصل الفوري إلى تطبيقاتهم.