# HTTP

[[English]](readme.md)

مكتبة لإنشاء خوادم بروتوكول نقل النص الترابطي (HTTP) مع دعم WebSocket بلغة الأسس. تعتمد هذه المكتبة على مكتبة civetweb.

## الإضافة إلى المشروع

أضف المكتبة لمشروعك باستخدام مدير الحزم:

<div dir=rtl>

```
اشمل "مـحا"؛
مـحا.اشمل_حزمة("Alusus/Http@0.3"، "بـننت.أسس")؛
```

</div>

```
import "Apm.alusus";
Apm.importPackage("Alusus/Http@0.3");
```

## مثال

<div dir=rtl>

```
اشمل "مـتم/طـرفية.أسس"؛
اشمل "مـتم/نـص.أسس"؛
اشمل "مـتم/ذاكـرة.أسس"؛
اشمل "مـحا"؛
مـحا.اشمل_حزمة("Alusus/Http@0.3"، "بـننت.أسس")؛

وحدة أخـتبار_الوحدة {
    استخدم مـتم؛

    دالة ابدا() {
        // نقوم بتعريف متغير من النمط مؤشر على سـياق بحيث يمثل السياق الخاص بالسيرفر
        // تم إنشاء السيرفر لينفذ التابع المعطى على المنفذ 8080
        عرف سياق: مؤشر[بـننت.سـياق] = بـننت.شغل_الخادم(طلب_المنادى~مؤشر, "8080")؛

        طـرفية.اطبع("شُغّل الخادم على المنفذ 8080.\جhttp://localhost:8080\جاضغط زر الإدخال لإغلاق الخادم.")؛
        // ننتظر أن يقوم المستخدم بالضغط على أي زر لإيقاف السيرفر
        طـرفية.أدخل_محرفا()؛
        // عندما نصل هنا يكون المستخدم قد ضغط على زر ما
        // بالتالي نقوم بإغلاق السيرفر
        بـننت.أوقف_الخادم(سياق)؛
        طـرفية.اطبع("أغلق الخادم.\جاضغط زر الإدخال للخروج.")؛
        طـرفية.أدخل_محرفا()؛
    }

    // الدالة التي سيقوم السيرفر بتنفيذها
    دالة طلب_المنادى (اتصال: مؤشر[بـننت.اتـصال]): صحيح{
        // نفوم بتعريف المتغير اللازم لتخزين معلومات الطلب الخاص بالاتصال المعطى
        عرف معلومات_الطلب: مؤشر[بـننت.مـعلومات_الطلب] = بـننت.هات_معلومات_الطلب(اتصال)؛
        // نقوم بتعريف متغير لتخزين المحتوى
        عرف سياق: مصفوفة[محرف, 1024]؛

        // نفوم بتهيئة المحتوى بكود html بسيط
        نـص.عيّن(
            سياق~مؤشر, "<div dir=rtl><h1>مرحبًا بك في خادم ب.ن.ن.ف للغة الأسس</h1><p>أنت الآن تتصفح \"%s\"</p></div>",
            معلومات_الطلب~محتوى.المعرف_المحلي
        )؛

        // نقوم بكتابة بيانات عبر الاتصال المعطى
        // هذه البيانات تمثل بعض المعلومات عن الطلب بالإضافة للمحتوى
        بـننت.اطبع(اتصال, "HTTP/1.1 200 OK\r\n")؛
        بـننت.اطبع(اتصال,  "Content-Type: text/html; charset=utf-8\r\n")؛
        بـننت.اطبع(اتصال,  "Content-Length: %d\r\n\r\n", نـص.هات_الطول(سياق~مؤشر))؛
        بـننت.اطبع(اتصال, سياق~مؤشر)؛

        أرجع 1؛
    }
}

أخـتبار_الوحدة.ابدا()؛
```

</div>

```
import "Srl/Console.alusus";
import "Srl/String.alusus";
import "Srl/Memory.alusus";
import "Apm.alusus";
Apm.importPackage("Alusus/Http@0.3");

module TestModule {
    use Srl;

    func start() {
        
        // نقوم بتعريف متغير من النمط مؤشر على سـياق بحيث يمثل السياق الخاص بالسيرفر
        // تم إنشاء السيرفر لينفذ التابع المعطى على المنفذ 8080
        def context: ptr[Http.Context] = Http.startServer(callbackRequest~ptr, "8080");

        Console.print("Server is listening on port 8080\nhttp://localhost:8080/\nPress enter to close server.");
        // ننتظر أن يقوم المستخدم بالضغط على أي زر لإيقاف السيرفر
        Console.getChar();
        // عندما نصل هنا يكون المستخدم قد ضغط على زر ما
        // بالتالي نقوم بإغلاق السيرفر
        Http.stopServer(context);

        Console.print("Server closed.\nPress enter to exit.");
        Console.getChar();
    };

    // الدالة التي سيقوم السيرفر بتنفيذها
    func callbackRequest(connection: ptr[Http.Connection]): Int {
        // نفوم بتعريف المتغير اللازم لتخزين معلومات الطلب الخاص بالاتصال المعطى
        def requestInfo: ptr[Http.RequestInfo] = Http.getRequestInfo(connection);
        // نقوم بتعريف متغير لتخزين المحتوى
        def content: array[Char, 1024];

        // نفوم بتهيئة المحتوى بكود html بسيط
        String.assign(content~ptr, "<h1>Welcome from Alusus</h1><p> you are in \"%s\"", requestInfo~cnt.localUri);

        // نقوم بكتابة بيانات عبر الاتصال المعطى
        // هذه البيانات تمثل بعض المعلومات عن الطلب بالإضافة للمحتوى
        Http.print(connection, "HTTP/1.1 200 OK\r\n");
        Http.print(connection, "Content-Type: text/html\r\n");
        Http.print(connection, "Content-Length: %d\r\n\r\n", String.getLength(content~ptr));
        Http.print(connection, content~ptr);

        return 1;
    };
};

TestModule.start();
```

## دعم WebSocket

تتضمن هذه المكتبة الآن دعماً شاملاً لـ WebSocket للتواصل الثنائي الفوري. راجع [توثيق WebSocket الكامل](websocket_documentation.ar.md) للحصول على معلومات مفصلة.

### مثال سريع لـ WebSocket

<div dir=rtl>

```
// تسجيل معالج WebSocket
بـننت.حدد_معالج_ويب_سوكت(
    سياق,
    \u200F"/websocket",
    عند_الاتصال~مؤشر,
    عند_الاستعداد~مؤشر,
    عند_البيانات~مؤشر,
    عند_الإغلاق~مؤشر
)؛

// إرسال رسالة نصية
بـننت.اكتب_نصا_في_ويب_سوكت(اتصال, "مرحباً WebSocket!")؛
```

</div>

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

راجع مجد الأمثلة للحصول على مثال كامل.

## الأصناف والوظائف

### سـياق (Context)

<div dir=rtl>

```
صنف سـياق {
    عرف علم_التوقف: صحيح؛
}
```

</div>

```
class Context{
    def stopFlag: int;
};
```

يُستعمل هذا الصنف لتخزين المعلومات الخاصة بالسياق.

#### علم_التوقف (stopFlag)

<div dir=rtl>

```
عرف علم_التوقف: صحيح؛
```

</div>

```
def stopFlag: int
```

يحدد إن كان يجب إيقاف حلقة الأحداث.

### مـناديات (Callbacks)

<div dir=rtl>

```
صنف مـناديات {
    عرف بداية_الطلب: منادى_الطلب؛
    عرف نهاية_الطلب: مؤشر[دالة(اتصال:مؤشر[اتصال]، رمز_حالة_الرد: صحيح)]؛
    عرف تسجيل_رسالة: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مـؤشر_محارف): صحيح]؛
    عرف تسجيل_ولوج: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مـؤشر_محارف): صحيح]؛
    عرف تهيئة_طما:مؤشر[دالة(سياق_طما:مؤشر[فـراغ]، بيانات_المستخدم: مؤشر[فـراغ]): صحيح]؛
    عرف غلق_الاتصال: مؤشر[دالة(اتصال:مؤشر[اتصال]): فـراغ]؛
    عرف خطأ_بننف: مؤشر[دالة(اتصال:مؤشر[اتصال]، الحالة: صحيح، رسالة: مؤشر[مصفوفة[محرف]]): صحيح]؛
    عرف تهيئة_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فـراغ]؛
    عرف نهاية_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فـراغ]؛
    عرف تهيئة_الموضوع: مؤشر[دالة(سياق:مؤشر[سياق]، نمط_المسلك: صحيح): فـراغ]؛
}
```

</div>

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

يحتوي هذا الصنف على المناديات الأساسية التي يمكن استعمالها مع بروتوكول ب.ن.ن.ف.
كل منادى هو مؤشر إلى دالة يستدعيها السيرفر.

#### بداية_الطلب (beginRequest)

<div dir=rtl>

```
عرف بداية_الطلب: منادى_الطلب؛
```

</div>

```
def beginRequest: RequestCallback;
```

يستدعيها الخادم عند بدء طلب ما.

* `0`: في حال لم يتم معالجة الطلب.
* `1` ~ `99`: المكتبة تترك عملية معالجة الطلب للدالة.

#### نهاية_الطلب (endRequest)

<div dir=rtl>

```
عرف نهاية_الطلب: مؤشر[دالة(اتصال:مؤشر[اتصال]، رمز_حالة_الرد: صحيح)]؛
```

</div>

```
def endRequest: ptr[func (connection: ptr[Connection], replyStatusCode: Int)];
```

يُستدعى عند إنهاء الطلب على اتصال معين وبرمز حالة رد معين.

#### تسجيل_رسالة (logMessage)

<div dir=rtl>

```
عرف تسجيل_رسالة: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مـؤشر_محارف: صحيح]؛
```

</div>

```
def logMessage: ptr[func (connection: ptr[Connection], message: CharsPtr): Int];
```

يستعمل لتسجيل رسالة معينة على اتصال معين.

* `0`: يتم استعمال الروتين المعتاد لتسجيل الرسائل.
* `قيمة غير صفرية`: يتم اعتبار أن تسجيل الرسائل قد تم ولا يتم تسجيل رسائل أخرى.

#### تسجيل_ولوج (logAccess)

<div dir=rtl>

```
عرف تسجيل_ولوج: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مـؤشر_محارف): صحيح]؛
```

</div>

```
def logAccess: ptr[func (connection: ptr[Connection], message: CharsPtr): Int];
```

يستعمل لتسجيل رسالة على اتصال معين لتوضيح حالة الوصول.

* `0`: يتم استعمال الروتين المعتاد لتسجيل رسائل الوصول.
* `قيمة غير صفرية`: يتم اعتبار أن تسجيل رسائل الوصول قد تم و لا يتم تسجيل رسائل أخرى.

#### تهيئة_طما (initSsl)

<div dir=rtl>

```
عرف تهيئة_طما:مؤشر[دالة(سياق_طما:مؤشر[فـراغ]، بيانات_المستخدم: مؤشر[فـراغ]): صحيح]؛
```

</div>

```
def initSsl: ptr[func (sslContext: ptr[Void], userData: ptr[Void]): Int];
```

يُستدعى عند تهيئة بروتوكول طبقة المقابل الآمنة (SSL).

* `0`: يجب على المكتبة إعداد شهادة ط.م.آ.
* `1`: تم إعداد الشهادة ولا حاجة للمكتبة للقيام بذلك.
* `-1`: حدث خطأ أثناء تهيئة الطما.

#### غلق_الاتصال (connectionClose)

<div dir=rtl>

```
عرف غلق_الاتصال: مؤشر[دالة(اتصال:مؤشر[اتصال]): فـراغ]؛
```

</div>

```
def connectionClose: ptr[func (connection: ptr[Connection]): Void];
```

يُستدعى عند إغلاق اتصال.

#### خطأ_بننف (httpError)

<div dir=rtl>

```
عرف خطأ_بننف: مؤشر[دالة(اتصال:مؤشر[اتصال]، الحالة: صحيح، رسالة: مؤشر[مصفوفة[محرف]]): صحيح]؛
```

</div>

```
def httpError: ptr[func (connection: ptr[Connection], status: Int, msg: ptr[array[Char]]): Int];
```

يستدعى عند حصول خطأ على اتصال ما مع الحالة ورسالة توضح الخطأ.

* `0`: في حال قامت الدالة بإرسال صفحة الخطأ بنفسها.
* `قيمة غير صفرية`: في حال لم يتم إرسال صفحة الخطأ من قبل الدالة و بالتالي يقوم السيرفر بذلك.

#### تهيئة_السياق (initContext)

<div dir=rtl>

```
عرف تهيئة_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فـراغ]؛
```

</div>

```
def initContext: ptr[func (context: ptr[Context]): Void];
```

يُستدعى لتهيئة السياق المستعمل في مسلك ما.

#### نهاية_السياق (exitContext)

<div dir=rtl>

```
عرف نهاية_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فـراغ]؛
```

</div>

```
def exitContext: ptr[func (context: ptr[Context]): Void];
```

يُستدعى عند إنهاء سياق.

#### تهيئة_الموضوع (initThread)

<div dir=rtl>

```
عرف تهيئة_الموضوع: مؤشر[دالة(سياق:مؤشر[سياق]، نمط_المسلك: صحيح): فـراغ]؛
```

</div>

```
def initThread: ptr[func (context: ptr[Context], threadType: Int): Void];
```

يُستدعى لتهيئة مسلك بسياق ما مع تحديد نمط المسلك.

### مـعلومات_الطلب (RequestInfo)

<div dir=rtl>

```
صنف مـعلومات_الطلب {
    عرف طريقة_الطلب: مـؤشر_محارف؛
    عرف معرف_الطلب: مـؤشر_محارف؛
    عرف المعرف_المحلي: مـؤشر_محارف؛
    عرف إصدار_بننف: مـؤشر_محارف؛
    عرف نص_الاستعلام: مـؤشر_محارف؛
    عرف المستخدم_البعيد: مـؤشر_محارف؛
    عرف العنوان_البعيد: مصفوفة[محرف، 48]؛
    عرف طول_المحتوى: صحيح[64]؛
    عرف المنفذ_البعيد: صحيح؛
    عرف مشفر: صحيح؛
    عرف بيانات_المستخدم: مؤشر[فـراغ]؛
    عرف بيانات_الاتصال: مؤشر[فـراغ]؛
    عرف عدد_الترويسات: صحيح؛
    عرف ترويسات_بننف: مصفوفة[ترويسة، 64]؛
}
```

</div>

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

يستعمل هذا الصنف لتخزين المعلومات الأساسية عن طلب ما.

#### طريقة_الطلب (requestMethod)

<div dir=rtl>

```
عرف طريقة_الطلب: مـؤشر_محارف؛
```

</div>

```
def requestMethod: CharsPtr;
```

نوع الطلب المستخدم مثلاً: GET.

#### معرف_الطلب (requestUri)

<div dir=rtl>

```
عرف معرف_الطلب: مـؤشر_محارف؛
```

</div>

```
def requestUri: CharsPtr;
```

الرابط الذي نرسل عليه الطلب.

#### المعرف_المحلي (localUri)

<div dir=rtl>

```
عرف المعرف_المحلي: مـؤشر_محارف؛
```

</div>

```
def localUri: CharsPtr;
```

الرابط المحلي للطلب، يمكن استعماله من أجل الطلبات ضمن نفس الخادم.

#### إصدار_بننف (httpVersion)

<div dir=rtl>

```
عرف إصدار_بننف: مـؤشر_محارف؛
```

</div>

```
def httpVersion: CharsPtr;
```

نسخة البروتوكول المستعمل في هذا الطلب.

#### نص_الاستعلام (queryString)

<div dir=rtl>

```
عرف نص_الاستعلام: مـؤشر_محارف؛
```

</div>

```
def queryString: CharsPtr;
```

بارامترات الطلب الموجودة في الرابط.

#### المستخدم_البعيد (remoteUser)

<div dir=rtl>

```
عرف المستخدم_البعيد: مـؤشر_محارف؛
```

</div>

```
def remoteUser: CharsPtr;
```

المستخدم البعيد الذي نتعامل معه.

#### العنوان_البعيد (remoteAddr)

<div dir=rtl>

```
عرف العنوان_البعيد: مصفوفة[محرف، 48]؛
```

</div>

```
def remoteAddr: array[Char, 48];
```

عنوان ال IP للمستخدم البعيد.

#### طول_المحتوى (contentLength)

<div dir=rtl>

```
عرف طول_المحتوى: صحيح[64]؛
```

</div>

```
def contentLength: Int[64];
```

طول جسم الطلب مقدراً بالبايت، و يكون -1 في حال لم يكن الطول محدداً.

#### المنفذ_البعيد (remotePort)

<div dir=rtl>

```
عرف المنفذ_البعيد: صحيح؛
```

</div>

```
def remotePort: Int;
```

المنفذ المستعمل على جهاز المستخدم البعيد.

#### مشفر (isSsl)

<div dir=rtl>

```
عرف مشفر: صحيح؛
```

</div>

```
def isSsl: Int;
```

هل الاتصال مشفر بتقنية ط.م.آ؟

#### بيانات_المستخدم (userData)

<div dir=rtl>

```
عرف بيانات_المستخدم: مؤشر[فـراغ]؛
```

</div>

```
def userData: ptr[Void];
```

بيانات خاصة بالمستخدم تُمرر إلى دالة `شغل_الخادم`.

#### بيانات_الاتصال (connData)

<div dir=rtl>

```
عرف بيانات_الاتصال: مؤشر[فـراغ]؛
```

</div>

```
def connData: ptr[Void];
```

بيانات خاصة بالاتصال.

#### عدد_الترويسات (numberHeaders)

<div dir=rtl>

```
عرف عدد_الترويسات: صحيح؛
```

</div>

```
def numberHeaders: Int;
```

عدد ترويسات الطلب.

#### ترويسات_بننف (httpHeaders)

<div dir=rtl>

```
عرف ترويسات_بننف: مصفوفة[ترويسة، 64]؛
```

</div>

```
def httpHeaders: array[Header, 64];
```

الترويسات الخاصة بالطلب.

### تـرويسة (Header)

<div dir=rtl>

```
صنف تـرويسة {
    عرف اسم: مـؤشر_محارف؛
    عرف قيمة: مـؤشر_محارف؛
}
```

</div>

```
class Header {
    def name: CharsPtr;
    def value: CharsPtr;
};
```

يستعمل هذا الصنف لتخزين بيانات ترويسة للطلب.

#### اسم (name)

<div dir=rtl>

```
عرف اسم: مـؤشر_محارف؛
```

</div>

```
def name: CharsPtr
```

اسم الترويسة، و هي بمثابة مفتاح.

#### قيمة (value)

<div dir=rtl>

```
عرف قيمة: مـؤشر_محارف؛
```

</div>

```
def value: CharsPtr
```

قيمة الترويسة و هي بمثابة القيمة المخزنة بهذا المفتاح.

### اتـصال (Connection)

<div dir=rtl>

```
صنف اتـصال {
    عرف معلومات_الطلب: مؤشر[مـعلومات_الطلب]؛
    عرف سياق: مؤشر[سـياق]؛
    عرف طما: مؤشر؛
    عرف سياق_طما_للعميل: مؤشر؛
    عرف عميل: مؤشر؛
    عرف موعد_الاتصال: صحيح؛
    عرف وقت_الطلب: صحيح؛
    عرف عدد_البايت_المرسلة: صحيح[64]؛
    عرف حجم_المحتوى: صحيح[64]؛
    عرف المحتوى_المستهلك: صحيح[64]؛
    عرف مقطع: صحيح؛
    عرف باقي_التقطيع: كلمة[64]؛
    عرف صوان: مـؤشر_محارف؛
    عرف معلومات_المسار: مـؤشر_محارف؛
    عرف يجب_الاغلاق: صحيح؛
    عرف معالج_الاخطاء: صحيح؛
    عرف خطأ_داخلي: صحيح؛
    عرف حجم_الصوان: صحيح؛
    عرف حجم_الطلب: صحيح؛
    عرف حجم_البيانات: صحيح؛
    عرف رمز_الحالة: صحيح؛
    عرف خنق: صحيح؛
    عرف آخر_وقت_خنق: صحيح؛
    عرف آخر_بايتات_خنق: صحيح[64]؛
    عرف قفل_المزامنة: صحيح[64]؛
}
```

</div>

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

يستعمل هذا الصنف لتخزين المعلومات المختلفة عن الاتصال.

#### معلومات_الطلب (requestInfo)

<div dir=rtl>

```
عرف معلومات_الطلب: مؤشر[مـعلومات_الطلب]؛
```

</div>

```
def requestInfo: ptr[RequestInfo];
```

معلومات الطلب المرافق للاتصال.

#### سياق (context)

<div dir=rtl>

```
عرف سياق: مؤشر[سـياق]؛
```

</div>

```
def context: ptr[Context];
```

معلومات السياق المستعمل في هذا الاتصال.

#### طما (ssl)

<div dir=rtl>

```
عرف طما: مؤشر؛
```

</div>

```
def ssl: ptr;
```

واصف بروتوكول ط.م.ا.

#### سياق_طما_للعميل (clientSslContext)

<div dir=rtl>

```
عرف سياق_طما_للعميل: مؤشر؛
```

</div>

```
def clientSslContext: ptr;
```

السياق الخاص بالمستخدم وفق بروتوكول ط.م.ا.

#### عميل (client)

<div dir=rtl>

```
عرف عميل: مؤشر؛
```

</div>

```
def client: ptr;
```

العميل المتصل عبر هذا الاتصال.

#### موعد_الاتصال (connectionBirthTime)

<div dir=rtl>

```
عرف موعد_الاتصال: صحيح؛
```

</div>

```
def connectionBirthTime: Int;
```

الوقت الذي تم فيه إنشاء الاتصال.

#### وقت_الطلب (requestTime)

<div dir=rtl>

```
عرف وقت_الطلب: صحيح؛
```

</div>

```
def requestTime: Int;
```

الوقت الذي تم فيه إنشاء الاتصال بالنسبة لوقت الخادم.

#### عدد_البايت_المرسلة (numberBytesSent)

<div dir=rtl>

```
عرف عدد_البايت_المرسلة: صحيح[64]؛
```

</div>

```
def numberBytesSent: int[64];
```

عدد البايتات التي تم إرسالها إلى العميل.

#### حجم_المحتوى (contentLen)

<div dir=rtl>

```
عرف حجم_المحتوى: صحيح[64]؛
```

</div>

```
def contentLen: int[64];
```

قيمة الترويسة حجم_المحتوى.

#### المحتوى_المستهلك (consumedContent)

<div dir=rtl>

```
عرف المحتوى_المستهلك: صحيح[64]؛
```

</div>

```
def consumedContent: int[64];
```

عدد بايتات المحتوى التي تم قراءتها.

#### مقطع (isChunked)

<div dir=rtl>

```
عرف مقطع: صحيح؛
```

</div>

```
def isChunked: int;
```

هل تم تقطيع النقل، يأخذ القيم التالية:

* `0`: في حال لم يكن النقل مقطع.
* `1`: في حال كان النقل مقطع و يوجد بيانات لقراءتها.
* `2`: في حال كان النقل مقطع و تم الانتهاء من قراءة كل البيانات.

#### باقي_التقطيع (chunkRemainder)

<div dir=rtl>

```
عرف باقي_التقطيع: كلمة[64]؛
```

</div>

```
def chunkRemainder: word[64];
```

البيانات التي لم يتم قراءتها بعد من آخر مقطع.

#### صوان (buf)

<div dir=rtl>

```
عرف صوان: مـؤشر_محارف؛
```

</div>

```
def buf: CharsPtr;
```

صوان يستعمل من أجل البيانات التي يتم تلقيها.

#### معلومات_المسار (pathInfo)

<div dir=rtl>

```
عرف معلومات_المسار: مـؤشر_محارف؛
```

</div>

```
def pathInfo: CharsPtr;
```

المعلومات الخاصة بالمسار ضمن الرابط.

#### يجب_الاغلاق (mustClose)

<div dir=rtl>

```
عرف يجب_الاغلاق: صحيح؛
```

</div>

```
def mustClose: int;
```

هل يجب إغلاق الاتصال.

#### معالج_الاخطاء (inErrorHandler)

<div dir=rtl>

```
عرف معالج_الاخطاء: صحيح؛
```

</div>

```
def inErrorHandler: int;
```

هل تتم معالجة الأخطاء؟

#### خطأ_داخلي (internalError)

<div dir=rtl>

```
عرف خطأ_داخلي: صحيح؛
```

</div>

```
def internalError: int;
```

هل حدث خطأ أثناء معالجة الطلب؟

#### حجم_الصوان (bufSize)

<div dir=rtl>

```
عرف حجم_الصوان: صحيح؛
```

</div>

```
def bufSize: int;
```

حجم الصوان.

#### حجم_الطلب (requestLen)

<div dir=rtl>

```
عرف حجم_الطلب: صحيح؛
```

</div>

```
def requestLen: int;
```

الحجم الكلي بالبايتات للطلب بالإضافة للترويسات ضمن الصوان.

#### حجم_البيانات (dataLen)

<div dir=rtl>

```
عرف حجم_البيانات: صحيح؛
```

</div>

```
def dataLen: int;
```

الحجم الكلي بالبايتات للبيانات ضمن الصوان.

#### رمز_الحالة (statusCode)

<div dir=rtl>

```
عرف رمز_الحالة: صحيح؛
```

</div>

```
def statusCode: int;
```

رمز الحالة للرد ضمن بروتوكول ب.ن.ن.ف.

#### خنق (throttle)

<div dir=rtl>

```
عرف خنق: صحيح؛
```

</div>

```
def throttle: int;
```

قيمة الخنق.

#### آخر_وقت_خنق (lastThrottleTime)

<div dir=rtl>

```
عرف آخر_وقت_خنق: صحيح؛
```

</div>

```
def lastThrottleTime: int;
```

آخر وقت تم إرسال فيه بيانات مخنوقة.

#### آخر_بايتات_خنق (lastThrottleBytes)

<div dir=rtl>

```
عرف آخر_بايتات_خنق: صحيح[64]؛
```

</div>

```
def lastThrottleBytes: int[64];
```

البايتات التي تم إرسالها في هذه الثانية.

#### قفل_المزامنة (mutex)

<div dir=rtl>

```
عرف قفل_المزامنة: صحيح[64]؛
```

</div>

```
def mutex: int[64];
```

يمكن استعمالها لقفل الاتصال من أجل الوصول المتزامن الآمن.

### شغل_الخادم (startServer)

<div dir=rtl>

```
@تصدير[mg_start]
دالة شغل_الخادم(مناديات: مؤشر[مـناديات]، بيانات_المستخدم: مؤشر، خيارات: مؤشر[مـؤشر_محارف]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، خيارات: سند[مـتم.مـصفوفة[مـؤشر_محارف]]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، خيارات: سند[مـتم.مـصفوفة[مـؤشر_محارف]]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، عدد_الخيارات: صـحيح، خيارات: ...مـؤشر_محارف): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، عدد_الخيارات: صـحيح، خيارات: ...مـؤشر_محارف): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، منفذ: مـؤشر_محارف): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، منفذ: مـؤشر_محارف): مؤشر[سـياق]؛
```

</div>

```
@expname[mg_start]
func startServer(callbacks: ptr[Callbacks], userData: ptr, options: ptr[CharsPtr]): ptr[Context];
func startServer(callback: RequestCallback, userData: ptr, options: ref[Srl.Array[CharsPtr]]): ptr[Context];
func startServer(callback: RequestCallback, options: ref[Srl.Array[CharsPtr]]): ptr[Context];
func startServer(callback: RequestCallback, userData: ptr, optsCount: Int, opts: ...CharsPtr): ptr[Context];
func startServer(callback: RequestCallback, optsCount: Int, opts: ...CharsPtr): ptr[Context];
func startServer(callback: RequestCallback, userData: ptr, port: CharsPtr): ptr[Context];
func startServer(callback: RequestCallback, port: CharsPtr): ptr[Context];
```

تستخدم هذه الدالة لتهيئة الخادم و تشغيله. تُرجع مؤشراً إلى سياق الخادم، أو مؤشراً إلى لاشيء في حال فشل الإنشاء.
في حال كان أحد المناديات غير موجود فيتم استبداله بمنادي افتراضي من قبل الخادم.

* `مناديات` (`callbacks`): المناديات التي يمكن عن طريقها تحديد آلية عمل الخادم في حالات مختلفة و كيفية معالجته للطلبات.
* `منادي` (`callback`): المنادي الذي يقوم الخادم بتنفيذه عند استلام طلب.
* `بيانات_المستخدم` (`userData`): بيانات مستخدم اختيارية تُمرر لاحقا إلى المناديات عند استلام طلب HTTP.
* `خيارات` (`options`): خيارات خاصة بالخادم مثل المنفذ الذي سيستمع إليه الخادم. يمكن أن تكون مؤشراً خاماً أو مصفوفة.
* `عدد_الخيارات` (`optsCount`): عدد الخيارات الموجودة في `خيارات`.
* `خيارات` (`opts`): خيارات خاصة بالسيرفر مثل المنفذ الذي سيستمع إليه السيرفر.
* `منفذ` (`port`): المنفذ الذي سيستمع إليه السيرفر.

### أوقف_الخادم (stopServer)

<div dir=rtl>

```
@تصدير[mg_stop];
دالة أوقف_الخادم(سياق: مؤشر[سـياق]): فـراغ؛
```

</div>

```
@expname[mg_stop];
func stopServer(context: ptr[Context]): Void;
```

تستعمل هذه الدالة لإيقاف الخادم وتحرير أي موارد كان يستعملها، تنتظر هذه الدالة
حتى يتم إنهاء كل المسالك التي يتم تنفيذها و بعدها يقوم بتحرير الموارد و إغلاق الخادم.

* `سياق` (`context`): مؤشر إلى السياق الخاص بالخادم الذي نريد إغلاقه.

### اقرأ (read)

<div dir=rtl>

```
@تصدير[mg_read]؛
دالة اقرأ(اتصال: مؤشر[اتـصال]، صوان: مؤشر، حجم_صوان: صحيح): صحيح؛
```

</div>

```
@expname[mg_read];
func read(connection: ptr[Connection], buffer: ptr, bufferSize: Int): Int;
```

تقوم هذه الدالة بقراءة البيانات من الاتصال المشار إليه عن طريق `اتصال`.
يتم التعامل مع البيانات على أنها بيانات ثنائية و يتم تخزينها في الصوان المشار إليه من قبل `صوان`.
تُرجع عدد البايتات المقروءة عند النجاح، `0` عند إغلاق الاتصال من قبل الطرف الآخر، أو قيمة سالبة عند عدم وجود بيانات.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد قراءة البيانات منه.
* `صوان` (`buffer`): مؤشر إلى الصوان الذي نريد تخزين البيانات فيه.
* `حجم_صوان` (`bufferSize`): الحجم الأقصى بالبايتات للبيانات التي يمكن تخزينها في الصوان.

### اكتب (write)

<div dir=rtl>

```
@تصدير[mg_write]؛
دالة اكتب(اتصال: مؤشر[اتـصال]، صوان: مـؤشر_محارف، حجم_صوان: صـحيح): صـحيح؛
```

</div>

```
@expname[mg_write]
func write(connection: ptr[Connection], buffer: CharsPtr, bufferSize: Int): Int
```

تستعمل هذه الدالة لإرسال البيانات عبر اتصال ما.
تُرجع عدد البايتات المرسلة عند النجاح، أو `-1` عند الفشل.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال البيانات عبره.
* `صوان` (`buffer`): الصوان الذي يحوي البيانات التي نريد إرسالها.
* `حجم_صوان` (`bufferSize`): حجم الصوان المشار له ب `صوان` بالبايت.

### اطبع (print)

<div dir=rtl>

```
@تصدير[mg_printf]؛
دالة اطبع(اتصال: مؤشر[اتـصال]، تنسيق: مـؤشر_محارف، أي_معطيات_أخرى...): صـحيح؛
```

</div>

```
@expname[mg_printf];
func print(connection: ptr[Connection], format: CharsPtr, ...any): Int;
```

تستعمل هذه الدالة لإرسال رسائل منسقة عبر اتصال ما.
تُرجع عدد البايتات المرسلة عند النجاح، `0` عند إغلاق الاتصال، أو `-1` عند حدوث خطأ.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال الرسالة عبره.
* `تنسيق` (`format`): التنسيق الخاص بالرسالة المراد إرسالها.
* `أي معطيات أخرى`: المعطيات التي يحتاجها التنسيق ليشكل رسالة.

### أرسل_ملف (sendFile)

<div dir=rtl>

```
@تصدير[mg_send_file]؛
دالة أرسل_ملف(اتصال: مؤشر[اتـصال]، اسم_الملف: مـؤشر_محارف): فـراغ؛
```

</div>

```
@expname[mg_send_file];
func sendFile(connection: ptr[Connection], fileName: CharsPtr): Void;
```

تستعمل هذه الدالة لإرسال ملف عبر اتصال ما.
تقوم هذه الدالة تلقائياً بإضافة الترويسات اللازمة.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال الملف عبره.
* `اسم_الملف` (`fileName`): اسم الملف الذي نريد إرساله.

### هات_الارتباط (getCookie)

<div dir=rtl>

```
@تصدير[mg_get_cookie]؛
دالة هات_الارتباط(
    نص_الكعكات: مـؤشر_محارف، اسم_الكعكة: مـؤشر_محارف، محتوى_الكعكة: مـؤشر_محارف، حجم_الكعكة: كـلمة[64]
): صحيح؛
```

</div>

```
@expname[mg_get_cookie];
func getCookie(cookiesString: CharsPtr, cookieName: CharsPtr, outCookieContent: CharsPtr, outCookieSize: Word[64]): Int;
```

تستعمل هذه الدالة لجلب قيمة متغير معين ضمن كعكة ما.
تُرجع حجم الكعكة بالبايت عند النجاح، `-1` عند عدم إيجاد الكعكة، أو `-2` عند فشل التخزين في الصوان.

* `نص_الكعكات` (`cookiesString`): اسم الكعكة.
* `اسم_الكعكة` (`cookieName`): اسم المتغير ضمن الكعكة المشار له ب `نص_الكعكات`.
* `محتوى_الكعكة` (`outCookieContent`): الصوان الذي سيتم تخزين المحتوى ضمنه.
* `حجم_الكعكة` (`outCookieSize`): حجم الصوان المشار له ب `محتوى_الكعكة`.

### هات_الترويسة (getHeader)

<div dir=rtl>

```
@تصدير[mg_get_header]؛
دالة هات_الترويسة(اتصال: مؤشر[اتـصال]، اسم_الترويسة: مـؤشر_محارف): مـؤشر_محارف؛
```

</div>

```
@expname[mg_get_header];
func getHeader(connection: ptr[Connection], headerName: CharsPtr): CharsPtr;
```

تستعمل هذه الدالة لجلب الترويسة عبر اتصال ما.
تُرجع مؤشراً إلى قيمة الترويسة أو مؤشراً إلى لا شيء عند الفشل.

* `اتصال` (`connection`): الاتصال المراد جلب الترويسة عبره.
* `اسم_الترويسة` (`headerName`): اسم الترويسة المراد جلبها.

### هات_معلومات_الطلب (getRequestInfo)

<div dir=rtl>

```
@تصدير[mg_get_request_info]؛
دالة هات_معلومات_الطلب(اتصال: مؤشر[اتـصال]): مؤشر[اتـصال]؛
```

</div>

```
@expname[mg_get_request_info];
func getRequestInfo(connection: ptr[Connection]): ptr[RequestInfo];
```

تستعمل هذه الدالة لجلب معلومات الطلب عبر اتصال ما.
تُرجع مؤشراً إلى معلومات الطلب.

* `اتصال` (`connection`): الاتصال المراد جلب معلومات الطلب منه.

### هات_متغير (getVariable)

<div dir=rtl>

```
@تصدير[mg_get_var]؛
دالة هات_متغير(
    بيانات: مـؤشر_محارف، حجم_البيانات: صـحيح، اسم_المتغير: مـؤشر_محارف، متغير_الخرج: مـؤشر_محارف، حجم_متغير_الخرج: صـحيح
): صـحيح؛
```

</div>

```
@expname[mg_get_var]
func getVariable(data: CharsPtr, dataSize: Int, variableName: CharsPtr, outVariable: CharsPtr, outVariableSize: Int): Int
```

تستعمل هذه الدالة لجلب قيمة متغير ما من الخادم.
هذا المتغير تم تمريره إلى الخادم إما عن طريق طلب POST أي ضمن الجسم الخاص بالطلب،
أو عن طريق طلب GET ضمن الرابط الخاص بالطلب.
تُرجع حجم قيمة المتغير بالبايت عند النجاح، `-1` عند عدم إيجاد المتغير، أو `-2` عند فشل التخزين في الصوان.

* `بيانات` (`data`): البيانات التي تم تخزينها في الصوان من طلب POST أو من رابط طلب GET.
* `حجم_البيانات` (`dataSize`): حجم البيانات المشار إليها ب `بيانات`.
* `اسم_المتغير` (`variableName`): اسم المتغير الذي نريد جلب قيمته.
* `متغير_الخرج` (`outVariable`): صوان الخرج الذي نريد تخزين قيمة المتغير فيه.
* `حجم_متغير_الخرج` (`outVariableSize`): حجم صوان الخرج المشار له ب `متغير_الخرج`.

---

## الرخصة

حقوق النشر © 2026 سرمد خالد عبد الله

هذا المشروع مرخص بموجب رخصة غنو العمومية الصغرى الإصدار 3.0 (LGPL-3.0). راجع ملفات `COPYING` و `COPYING.LESSER` للحصول على التفاصيل.

