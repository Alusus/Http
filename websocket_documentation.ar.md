<div dir=rtl>

# توثيق دعم WebSocket

[[English]](websocket_documentation.en.md)

[[رجوع]](readme.ar.md)

## نظرة عامة

تتضمن وحدة HTTP في الأسس الآن دعماً شاملاً لـ WebSocket، مبني على مكتبة CivetWeb. توفر WebSockets قنوات اتصال كاملة الإرسال عبر اتصال TCP واحد، مما يتيح التواصل الثنائي الفوري بين العملاء والخوادم.

## الميزات

- **التواصل الفوري**: تبادل الرسائل الثنائي بين العميل والخادم
- **البنية المعتمدة على الأحداث**: معالجة الأحداث باستخدام دوال الاستدعاء
- **الرسائل النصية والثنائية**: دعم إرسال البيانات النصية والثنائية
- **إدارة الاتصالات**: معالجة تلقائية لمصافحة WebSocket ودورة حياة الاتصال
- **التكامل**: تكامل سلس مع وظائف خادم HTTP الحالية

## أنواع دوال الاستدعاء لـ WebSocket

### مـنادى_اتصال_ويب_سوكت (WebSocketConnectCallback)

```
عرف مـنادى_اتصال_ويب_سوكت: لقب مؤشر[دالة (اتصال: مؤشر[بـننف.اتـصال], بيانات_المستخدم: مؤشر[فـراغ]): صـحيح];
```

<div dir=ltr>

```alusus
def WebSocketConnectCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Int];
```

</div>

يتم استدعاؤها عند إنشاء اتصال WebSocket جديد. إرجاع 0 لقبول الاتصال، أو رقم غير صفر لرفضه.

### مـنادى_استعداد_ويب_سوكت (WebSocketReadyCallback)

```
عرف مـنادى_استعداد_ويب_سوكت: لقب مؤشر[دالة (اتصال: مؤشر[بـننف.اتـصال], بيانات_المستخدم: مؤشر[فـراغ]): فـراغ];
```

<div dir=ltr>

```alusus
def WebSocketReadyCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```

</div>

يتم استدعاؤها عندما يصبح اتصال WebSocket جاهزاً للتواصل.

### مـنادى_بيانات_ويب_سوكت (WebSocketDataCallback)

```
عرف مـنادى_بيانات_ويب_سوكت: لقب مؤشر[دالة (اتصال: مؤشر[بـننف.اتـصال], رمز_العملية: صـحيح, بيانات: مـؤشر_محارف, طول_البيانات: صـحيح, بيانات_المستخدم: مؤشر[فـراغ]): صـحيح];
```

<div dir=ltr>

```alusus
def WebSocketDataCallback: alias ptr[func (connection: ptr[Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int];
```

</div>

يتم استدعاؤها عند استلام البيانات من العميل. يشير المعامل `رمز_العملية` إلى نوع الإطار:
- `1`: إطار نصي
- `2`: إطار ثنائي
- `8`: إطار إغلاق
- `9`: إطار Ping
- `10`: إطار Pong

### مـنادى_إغلاق_ويب_سوكت (WebSocketCloseCallback)

```
عرف مـنادى_إغلاق_ويب_سوكت: لقب مؤشر[دالة (اتصال: مؤشر[بـننف.اتـصال], بيانات_المستخدم: مؤشر[فـراغ]): فـراغ];
```

<div dir=ltr>

```alusus
def WebSocketCloseCallback: alias ptr[func (connection: ptr[Connection], userData: ptr[Void]): Void];
```

</div>

يتم استدعاؤها عند إغلاق اتصال WebSocket.

## الدوال الأساسية

### حدد_معالج_ويب_سوكت (setWebSocketHandler)

```
دالة حدد_معالج_ويب_سوكت(
    سياق: مؤشر[بـننف.سـياق],
    مسار: مـؤشر_محارف,
    معالج_الاتصال: مـنادى_اتصال_ويب_سوكت,
    معالج_الاستعداد: مـنادى_استعداد_ويب_سوكت,
    معالج_البيانات: مـنادى_بيانات_ويب_سوكت,
    معالج_الإغلاق: مـنادى_إغلاق_ويب_سوكت,
    بيانات_المستخدم: مؤشر[فـراغ]
): فـراغ;
```

<div dir=ltr>

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

</div>

تسجل معالجات WebSocket لمسار URI محدد.

**المعاملات:**
- `سياق`: سياق الخادم المُرجع من `شغل_الخادم()`
- `مسار`: مسار URI لنقطة WebSocket (مثل "/websocket")
- `معالج_الاتصال`: دالة الاستدعاء للاتصالات الجديدة
- `معالج_الاستعداد`: دالة الاستدعاء عندما يصبح الاتصال جاهزاً
- `معالج_البيانات`: دالة الاستدعاء للرسائل الواردة
- `معالج_الإغلاق`: دالة الاستدعاء عند إغلاق الاتصال
- `بيانات_المستخدم`: بيانات المستخدم الاختيارية الممررة إلى دوال الاستدعاء

### حدد_معالج_ويب_سوكت_مع_البروتوكولات_الفرعية (setWebSocketHandlerWithSubprotocols)

```
دالة حدد_معالج_ويب_سوكت_مع_البروتوكولات_الفرعية(
    سياق: مؤشر[بـننف.سـياق],
    مسار: مـؤشر_محارف,
    البروتوكولات_الفرعية: مؤشر[مـؤشر_محارف],
    معالج_الاتصال: مـنادى_اتصال_ويب_سوكت,
    معالج_الاستعداد: مـنادى_استعداد_ويب_سوكت,
    معالج_البيانات: مـنادى_بيانات_ويب_سوكت,
    معالج_الإغلاق: مـنادى_إغلاق_ويب_سوكت,
    بيانات_المستخدم: مؤشر[فـراغ]
): فـراغ;
```

<div dir=ltr>

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

</div>

مثل `حدد_معالج_ويب_سوكت` لكن مع دعم البروتوكولات الفرعية لـ WebSocket.

## دوال إرسال الرسائل

### اكتب_في_ويب_سوكت (writeToWebSocket)

```
دالة اكتب_في_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال], رمز_العملية: صـحيح, بيانات: مـؤشر_محارف, طول_البيانات: صـحيح): صـحيح;
```

<div dir=ltr>

```alusus
func writeToWebSocket(connection: ptr[Connection], opcode: Int, data: CharsPtr, dataLen: Int): Int;
```

</div>

إرسال إطار WebSocket خام مع رمز العملية المحدد.

**رموز العمليات:**
- `1`: إطار نصي
- `2`: إطار ثنائي
- `8`: إطار إغلاق
- `9`: إطار Ping
- `10`: إطار Pong

### اكتب_نصا_في_ويب_سوكت (writeTextToWebSocket)

```
دالة اكتب_نصا_في_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال], بيانات: مـؤشر_محارف, طول_البيانات: صـحيح): صـحيح;
```

<div dir=ltr>

```alusus
func writeTextToWebSocket(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```

</div>

إرسال رسالة نصية إلى عميل WebSocket.

### اكتب_ثنائيا_في_ويب_سوكت (writeBinaryToWebSocket)

```
دالة اكتب_ثنائيا_في_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال], بيانات: مـؤشر_محارف, طول_البيانات: صـحيح): صـحيح;
```

<div dir=ltr>

```alusus
func writeBinaryToWebSocket(connection: ptr[Connection], data: CharsPtr, dataLen: Int): Int;
```

</div>

إرسال رسالة ثنائية إلى عميل WebSocket.

### أغلق_ويب_سوكت (closeWebSocket)

```
دالة أغلق_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال]): صـحيح;
```

<div dir=ltr>

```alusus
func closeWebSocket(connection: ptr[Connection]): Int;
```

</div>

إغلاق اتصال WebSocket بطريقة لائقة.

## مثال على الاستخدام

```أسس
اشمل "مـتم/طـرفية.أسس"؛
اشمل "مـتم/نـص.أسس"؛
اشمل "مـحا"؛
مـحا.اشمل_ملف("Alusus/Http"، "بـننف.أسس")؛

وحدة مـثال_ويب_سوكت {
    استخدم مـتم؛

    دالة ابدأ() {
        // بدء تشغيل خادم HTTP
        عرف سياق: مؤشر[بـننف.سـياق] = بـننف.شغل_الخادم(معالج_طلب_بننف~مؤشر، "8080")؛

        // تسجيل معالج WebSocket
        بـننف.حدد_معالج_ويب_سوكت(
            سياق،
            ‏"/websocket"،
            عند_اتصال_ويب_سوكت~مؤشر،
            عند_استعداد_ويب_سوكت~مؤشر،
            عند_بيانات_ويب_سوكت~مؤشر،
            عند_إغلاق_ويب_سوكت~مؤشر
        )؛

        طـرفية.اطبع("الخادم يستمع على المنفذ 8080\ج")؛
        طـرفية.أدخل_محرفا()؛
        بـننف.أوقف_الخادم(سياق)؛
    }

    دالة معالج_طلب_بننف(اتصال: مؤشر[بـننف.اتـصال]): صـحيح {
        عرف معلومات_الطلب: مؤشر[بـننف.مـعلومات_الطلب] = بـننف.هات_معلومات_الطلب(اتصال)؛
        إذا نـص.أمتطابق(معلومات_الطلب~محتوى.المعرف_المحلي، "/websocket") {
            أرجع 0؛
        }

        // معالجة طلبات HTTP العادية
        بـننف.اطبع(اتصال، "HTTP/1.1 200 OK\r\n")؛
        بـننف.اطبع(اتصال، "Content-Type: text/html; charset=utf-8\r\n\r\n")؛
        بـننف.اطبع(اتصال، "<h1>خادم WebSocket</h1>")؛
        أرجع 1؛
    }

    دالة عند_اتصال_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال]، بيانات_المستخدم: مؤشر[فـراغ]): صـحيح {
        طـرفية.اطبع("عميل WebSocket متصل\ج")؛
        أرجع 0؛ // قبول الاتصال
    }

    دالة عند_استعداد_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال]، بيانات_المستخدم: مؤشر[فـراغ]): فـراغ {
        طـرفية.اطبع("WebSocket جاهز\ج")؛
        بـننف.اكتب_نصا_في_ويب_سوكت(اتصال، "أهلاً وسهلاً!")؛
    }

    دالة عند_بيانات_ويب_سوكت(
        اتصال: مؤشر[بـننف.اتـصال]،
        رمز_العملية: صـحيح،
        بيانات: مـؤشر_محارف،
        طول_البيانات: صـحيح،
        بيانات_المستخدم: مؤشر[فـراغ]
    ): صـحيح {
        إذا رمز_العملية == 1 { // إطار نصي
            طـرفية.اطبع("تم الاستلام: ")؛
            طـرفية.اطبع(بيانات، طول_البيانات)؛
            طـرفية.اطبع("\ج")؛

            // إرجاع الرسالة
            بـننف.اكتب_نصا_في_ويب_سوكت(اتصال، بيانات، طول_البيانات)؛
        }
        أرجع 1؛
    }

    دالة عند_إغلاق_ويب_سوكت(اتصال: مؤشر[بـننف.اتـصال]، بيانات_المستخدم: مؤشر[فـراغ]): فـراغ {
        طـرفية.اطبع("عميل WebSocket منقطع\ج")؛
    }
}

مـثال_ويب_سوكت.ابدأ()؛
```

<div dir=ltr>

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
        def req: ptr[Http.RequestInfo] = Http.getRequestInfo(connection);
        if String.isEqual(req~cnt.localUri, "/websocket") return 0;

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
        Http.writeTextToWebSocket(connection, "أهلاً وسهلاً!");
    };

    func onData(connection: ptr[Http.Connection], bits: Int, data: CharsPtr, dataLen: Int, userData: ptr[Void]): Int {
        if bits == 1 { // إطار نصي
            Console.print("تم الاستلام: ");
            Console.print(data, dataLen);
            Console.print("\n");

            // إرجاع الرسالة
            Http.writeTextToWebSocket(connection, data, dataLen);
        }
        return 1;
    };

    func onClose(connection: ptr[Http.Connection], userData: ptr[Void]): Void {
        Console.print("عميل WebSocket منقطع\n");
    };
};

WebSocketExample.start();
```

</div>

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

</div>

