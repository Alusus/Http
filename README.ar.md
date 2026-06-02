# HTTP

[[English]](README.md)

<div dir=rtl>

مكتبة لإنشاء خوادم بروتوكول نقل النص الترابطي (HTTP) مع دعم WebSocket بلغة الأسس. تعتمد هذه المكتبة على مكتبة civetweb.

## الإضافة إلى المشروع

أضف المكتبة لمشروعك باستخدام مدير الحزم:

```
اشمل "مـحا"؛
مـحا.اشمل_حزمة("Alusus/Http@0.3"، "بـننت.أسس")؛
```

<div dir=ltr>

```
import "Apm.alusus";
Apm.importPackage("Alusus/Http@0.3");
```

</div>

## مثال

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

<div dir=ltr>

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

</div>

## دعم WebSocket

تتضمن هذه المكتبة الآن دعماً شاملاً لـ WebSocket للتواصل الثنائي الفوري. راجع [توثيق WebSocket الكامل](websocket_documentation.ar.md) للحصول على معلومات مفصلة.

### مثال سريع لـ WebSocket

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

<div dir=ltr>

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

</div>

راجع مجد الأمثلة للحصول على مثال كامل.

## الأصناف والوظائف

### سـياق (Context)

```
صنف سـياق {
    عرف علم_التوقف: صحيح؛
}
```

<div dir=ltr>

```
class Context{
    def stopFlag: int;
};
```

</div>

يُستعمل هذا الصنف لتخزين المعلومات الخاصة بالسياق.

#### علم_التوقف (stopFlag)

```
عرف علم_التوقف: صحيح؛
```

<div dir=ltr>

```
def stopFlag: int
```

</div>

يحدد إن كان يجب إيقاف حلقة الأحداث.


### مـناديات (Callbacks)

```
صنف مـناديات {
    عرف بداية_الطلب: منادى_الطلب؛
    عرف نهاية_الطلب: مؤشر[دالة(اتصال:مؤشر[اتصال]، رمز_حالة_الرد: صحيح): صحيح]؛
    عرف تسجيل_رسالة: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مؤشر[محرف]): صحيح]؛
    عرف تسجيل_ولوج: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مؤشر[محرف]): صحيح]؛
    عرف تهيئة_طما:مؤشر[دالة(سياق_طما:مؤشر[فراغ]، بيانات_المستخدم: مؤشر[فراغ]): صحيح]؛
    عرف غلق_الاتصال: مؤشر[دالة(اتصال:مؤشر[اتصال]): فراغ]؛
    عرف خطأ_بننف: مؤشر[دالة(اتصال:مؤشر[اتصال]، الحالة: صحيح، رسالة: مؤشر[مصفوفة[محرف]]): صحيح]؛
    عرف تهيئة_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فراغ]؛
    عرف نهاية_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فراغ]؛
    عرف تهيئة_الموضوع: مؤشر[دالة(سياق:مؤشر[سياق]، نمط_المسلك: صحيح): فراغ]؛
}
```

<div dir=ltr>

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

</div>

يحتوي هذا الصنف على المناديات الأساسية التي يمكن استعمالها مع بروتوكول ب.ن.ن.ف.
كل منادى هو مؤشر إلى دالة يستدعيها السيرفر.

#### بداية_الطلب (beginRequest)

```
عرف بداية_الطلب: منادى_الطلب؛
```

<div dir=ltr>

```
def beginRequest: RequestCallback
```

</div>

يستدعيها الخادم عند بدء طلب ما.

* `0`: في حال لم يتم معالجة الطلب.
* `1` ~ `99`: المكتبة تترك عملية معالجة الطلب للدالة.

#### نهاية_الطلب (endRequest)

```
عرف نهاية_الطلب: مؤشر[دالة(اتصال:مؤشر[اتصال]، رمز_حالة_الرد: صحيح): صحيح]؛
```

<div dir=ltr>

```
def endRequest: ptr[func (connection: ptr[Connection], replyStatusCode: Int)]
```

</div>

يُستدعى عند إنهاء الطلب على اتصال معين وبرمز حالة رد معين.

#### تسجيل_رسالة (logMessage)

```
عرف تسجيل_رسالة: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مؤشر[محرف]): صحيح]؛
```

<div dir=ltr>

```
def logMessage: ptr[func (connection: ptr[Connection], message: CharsPtr): Int]
```

</div>

يستعمل لتسجيل رسالة معينة على اتصال معين.

* `0`: يتم استعمال الروتين المعتاد لتسجيل الرسائل.
* `قيمة غير صفرية`: يتم اعتبار أن تسجيل الرسائل قد تم ولا يتم تسجيل رسائل أخرى.

#### تسجيل_ولوج (logAccess)

```
عرف تسجيل_ولوج: مؤشر[دالة(اتصال:مؤشر[اتصال]، رسالة: مؤشر[محرف]): صحيح]؛
```

<div dir=ltr>

```
def logAccess: ptr[func (connection: ptr[Connection], message: CharsPtr): Int]
```

</div>

يستعمل لتسجيل رسالة على اتصال معين لتوضيح حالة الوصول.

* `0`: يتم استعمال الروتين المعتاد لتسجيل رسائل الوصول.
* `قيمة غير صفرية`: يتم اعتبار أن تسجيل رسائل الوصول قد تم و لا يتم تسجيل رسائل أخرى.

#### تهيئة_طما (initSsl)

```
عرف تهيئة_طما:مؤشر[دالة(سياق_طما:مؤشر[فراغ]، بيانات_المستخدم: مؤشر[فراغ]): صحيح]؛
```

<div dir=ltr>

```
def initSsl: ptr[func (sslContext: ptr[Void], userData: ptr[Void]): Int]
```

</div>

يُستدعى عند تهيئة بروتوكول طبقة المقابل الآمنة (SSL).

* `0`: يجب على المكتبة إعداد شهادة ط.م.آ.
* `1`: تم إعداد الشهادة ولا حاجة للمكتبة للقيام بذلك.
* `-1`: حدث خطأ أثناء تهيئة الطما.

#### غلق_الاتصال (connectionClose)

```
عرف غلق_الاتصال: مؤشر[دالة(اتصال:مؤشر[اتصال]): فراغ]؛
```

<div dir=ltr>

```
def connectionClose: ptr[func (connection: ptr[Connection]): Void]
```

</div>

يُستدعى عند إغلاق اتصال.

#### خطأ_بننف (httpError)

```
عرف خطأ_بننف: مؤشر[دالة(اتصال:مؤشر[اتصال]، الحالة: صحيح، رسالة: مؤشر[مصفوفة[محرف]]): صحيح]؛
```

<div dir=ltr>

```
def httpError: ptr[func (connection: ptr[Connection], status: Int, msg: ptr[array[Char]]): Int]
```

</div>

يستدعى عند حصول خطأ على اتصال ما مع الحالة ورسالة توضح الخطأ.

* `0`: في حال قامت الدالة بإرسال صفحة الخطأ بنفسها.
* `قيمة غير صفرية`: في حال لم يتم إرسال صفحة الخطأ من قبل الدالة و بالتالي يقوم السيرفر بذلك.

#### تهيئة_السياق (initContext)

```
عرف تهيئة_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فراغ]؛
```

<div dir=ltr>

```
def initContext: ptr[func (context: ptr[Context]): Void]
```

</div>

يُستدعى لتهيئة السياق المستعمل في مسلك ما.

#### نهاية_السياق (exitContext)

```
عرف نهاية_السياق: مؤشر[دالة(سياق:مؤشر[سياق]): فراغ]؛
```

<div dir=ltr>

```
def exitContext: ptr[func (context: ptr[Context]): Void]
```

</div>

يُستدعى عند إنهاء سياق.

#### تهيئة_الموضوع (initThread)

```
عرف تهيئة_الموضوع: مؤشر[دالة(سياق:مؤشر[سياق]، نمط_المسلك: صحيح): فراغ]؛
```

<div dir=ltr>

```
def initThread: ptr[func (context: ptr[Context], threadType: Int): Void]
```

</div>

يُستدعى لتهيئة مسلك بسياق ما مع تحديد نمط المسلك.


### مـعلومات_الطلب (RequestInfo)

```
صنف مـعلومات_الطلب {
    عرف طريقة_الطلب: مؤشر[محرف]؛
    عرف معرف_الطلب: مؤشر[محرف]؛
    عرف المعرف_المحلي: مؤشر[محرف]؛
    عرف إصدار_بننف: مؤشر[محرف]؛
    عرف نص_الاستعلام: مؤشر[محرف]؛
    عرف المستخدم_البعيد: مؤشر[محرف]؛
    عرف العنوان_البعيد: مصفوفة[محرف، 48]؛
    عرف طول_المحتوى: صحيح[64]؛
    عرف المنفذ_البعيد: صحيح؛
    عرف مشفر: صحيح؛
    عرف بيانات_المستخدم: مؤشر[فراغ]؛
    عرف بيانات_الاتصال: مؤشر[فراغ]؛
    عرف عدد_الترويسات: صحيح؛
    عرف ترويسات_بننف: مصفوفة[ترويسة، 64]؛
}
```

<div dir=ltr>

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

</div>

يستعمل هذا الصنف لتخزين المعلومات الأساسية عن طلب ما.

#### طريقة_الطلب (requestMethod)

```
عرف طريقة_الطلب: مؤشر[محرف]؛
```

<div dir=ltr>

```
def requestMethod: CharsPtr
```

</div>

نوع الطلب المستخدم مثلاً: GET.

#### معرف_الطلب (requestUri)

```
عرف معرف_الطلب: مؤشر[محرف]؛
```

<div dir=ltr>

```
def requestUri: CharsPtr
```

</div>

الرابط الذي نرسل عليه الطلب.

#### المعرف_المحلي (localUri)

```
عرف المعرف_المحلي: مؤشر[محرف]؛
```

<div dir=ltr>

```
def localUri: CharsPtr
```

</div>

الرابط المحلي للطلب، يمكن استعماله من أجل الطلبات ضمن نفس الخادم.

#### إصدار_بننف (httpVersion)

```
عرف إصدار_بننف: مؤشر[محرف]؛
```

<div dir=ltr>

```
def httpVersion: CharsPtr
```

</div>

نسخة البروتوكول المستعمل في هذا الطلب.

#### نص_الاستعلام (queryString)

```
عرف نص_الاستعلام: مؤشر[محرف]؛
```

<div dir=ltr>

```
def queryString: CharsPtr
```

</div>

بارامترات الطلب الموجودة في الرابط.

#### المستخدم_البعيد (remoteUser)

```
عرف المستخدم_البعيد: مؤشر[محرف]؛
```

<div dir=ltr>

```
def remoteUser: CharsPtr
```

</div>

المستخدم البعيد الذي نتعامل معه.

#### العنوان_البعيد (remoteAddr)

```
عرف العنوان_البعيد: مصفوفة[محرف، 48]؛
```

<div dir=ltr>

```
def remoteAddr: array[Char, 48]
```

</div>

عنوان ال IP للمستخدم البعيد.

#### طول_المحتوى (contentLength)

```
عرف طول_المحتوى: صحيح[64]؛
```

<div dir=ltr>

```
def contentLength: Int[64]
```

</div>

طول جسم الطلب مقدراً بالبايت، و يكون -1 في حال لم يكن الطول محدداً.

#### المنفذ_البعيد (remotePort)

```
عرف المنفذ_البعيد: صحيح؛
```

<div dir=ltr>

```
def remotePort: Int
```

</div>

المنفذ المستعمل على جهاز المستخدم البعيد.

#### مشفر (isSsl)

```
عرف مشفر: صحيح؛
```

<div dir=ltr>

```
def isSsl: Int
```

</div>

هل الاتصال مشفر بتقنية ط.م.آ؟

#### بيانات_المستخدم (userData)

```
عرف بيانات_المستخدم: مؤشر[فراغ]؛
```

<div dir=ltr>

```
def userData: ptr[Void]
```

</div>

بيانات خاصة بالمستخدم تُمرر إلى دالة `شغل_الخادم`.

#### بيانات_الاتصال (connData)

```
عرف بيانات_الاتصال: مؤشر[فراغ]؛
```

<div dir=ltr>

```
def connData: ptr[Void]
```

</div>

بيانات خاصة بالاتصال.

#### عدد_الترويسات (numberHeaders)

```
عرف عدد_الترويسات: صحيح؛
```

<div dir=ltr>

```
def numberHeaders: Int
```

</div>

عدد ترويسات الطلب.

#### ترويسات_بننف (httpHeaders)

```
عرف ترويسات_بننف: مصفوفة[ترويسة، 64]؛
```

<div dir=ltr>

```
def httpHeaders: array[Header, 64]
```

</div>

الترويسات الخاصة بالطلب.


### تـرويسة (Header)

```
صنف تـرويسة {
    عرف اسم: مؤشر[محرف]؛
    عرف قيمة: مؤشر[محرف]؛
}
```

<div dir=ltr>

```
class Header {
    def name: CharsPtr;
    def value: CharsPtr;
};
```

</div>

يستعمل هذا الصنف لتخزين بيانات ترويسة للطلب.

#### اسم (name)

```
عرف اسم: مؤشر[محرف]؛
```

<div dir=ltr>

```
def name: CharsPtr
```

</div>

اسم الترويسة، و هي بمثابة مفتاح.

#### قيمة (value)

```
عرف قيمة: مؤشر[محرف]؛
```

<div dir=ltr>

```
def value: CharsPtr
```

</div>

قيمة الترويسة و هي بمثابة القيمة المخزنة بهذا المفتاح.


### اتـصال (Connection)

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
    عرف صوان: مؤشر[محرف]؛
    عرف معلومات_المسار: مؤشر[محرف]؛
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

<div dir=ltr>

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

</div>

يستعمل هذا الصنف لتخزين المعلومات المختلفة عن الاتصال.

#### معلومات_الطلب (requestInfo)

```
عرف معلومات_الطلب: مؤشر[مـعلومات_الطلب]؛
```

<div dir=ltr>

```
def requestInfo: ptr[RequestInfo]
```

</div>

معلومات الطلب المرافق للاتصال.

#### سياق (context)

```
عرف سياق: مؤشر[سـياق]؛
```

<div dir=ltr>

```
def context: ptr[Context]
```

</div>

معلومات السياق المستعمل في هذا الاتصال.

#### طما (ssl)

```
عرف طما: مؤشر؛
```

<div dir=ltr>

```
def ssl: ptr
```

</div>

واصف بروتوكول ط.م.ا.

#### سياق_طما_للعميل (clientSslContext)

```
عرف سياق_طما_للعميل: مؤشر؛
```

<div dir=ltr>

```
def clientSslContext: ptr
```

</div>

السياق الخاص بالمستخدم وفق بروتوكول ط.م.ا.

#### عميل (client)

```
عرف عميل: مؤشر؛
```

<div dir=ltr>

```
def client: ptr
```

</div>

العميل المتصل عبر هذا الاتصال.

#### موعد_الاتصال (connectionBirthTime)

```
عرف موعد_الاتصال: صحيح؛
```

<div dir=ltr>

```
def connectionBirthTime: Int
```

</div>

الوقت الذي تم فيه إنشاء الاتصال.

#### وقت_الطلب (requestTime)

```
عرف وقت_الطلب: صحيح؛
```

<div dir=ltr>

```
def requestTime: Int
```

</div>

الوقت الذي تم فيه إنشاء الاتصال بالنسبة لوقت الخادم.

#### عدد_البايت_المرسلة (numberBytesSent)

```
عرف عدد_البايت_المرسلة: صحيح[64]؛
```

<div dir=ltr>

```
def numberBytesSent: int[64]
```

</div>

عدد البايتات التي تم إرسالها إلى العميل.

#### حجم_المحتوى (contentLen)

```
عرف حجم_المحتوى: صحيح[64]؛
```

<div dir=ltr>

```
def contentLen: int[64]
```

</div>

قيمة الترويسة حجم_المحتوى.

#### المحتوى_المستهلك (consumedContent)

```
عرف المحتوى_المستهلك: صحيح[64]؛
```

<div dir=ltr>

```
def consumedContent: int[64]
```

</div>

عدد بايتات المحتوى التي تم قراءتها.

#### مقطع (isChunked)

```
عرف مقطع: صحيح؛
```

<div dir=ltr>

```
def isChunked: int
```

</div>

هل تم تقطيع النقل، يأخذ القيم التالية:

* `0`: في حال لم يكن النقل مقطع.
* `1`: في حال كان النقل مقطع و يوجد بيانات لقراءتها.
* `2`: في حال كان النقل مقطع و تم الانتهاء من قراءة كل البيانات.

#### باقي_التقطيع (chunkRemainder)

```
عرف باقي_التقطيع: كلمة[64]؛
```

<div dir=ltr>

```
def chunkRemainder: word[64]
```

</div>

البيانات التي لم يتم قراءتها بعد من آخر مقطع.

#### صوان (buf)

```
عرف صوان: مؤشر[محرف]؛
```

<div dir=ltr>

```
def buf: CharsPtr
```

</div>

صوان يستعمل من أجل البيانات التي يتم تلقيها.

#### معلومات_المسار (pathInfo)

```
عرف معلومات_المسار: مؤشر[محرف]؛
```

<div dir=ltr>

```
def pathInfo: CharsPtr
```

</div>

المعلومات الخاصة بالمسار ضمن الرابط.

#### يجب_الاغلاق (mustClose)

```
عرف يجب_الاغلاق: صحيح؛
```

<div dir=ltr>

```
def mustClose: int
```

</div>

هل يجب إغلاق الاتصال.

#### معالج_الاخطاء (inErrorHandler)

```
عرف معالج_الاخطاء: صحيح؛
```

<div dir=ltr>

```
def inErrorHandler: int
```

</div>

هل تتم معالجة الأخطاء؟

#### خطأ_داخلي (internalError)

```
عرف خطأ_داخلي: صحيح؛
```

<div dir=ltr>

```
def internalError: int
```

</div>

هل حدث خطأ أثناء معالجة الطلب؟

#### حجم_الصوان (bufSize)

```
عرف حجم_الصوان: صحيح؛
```

<div dir=ltr>

```
def bufSize: int
```

</div>

حجم الصوان.

#### حجم_الطلب (requestLen)

```
عرف حجم_الطلب: صحيح؛
```

<div dir=ltr>

```
def requestLen: int
```

</div>

الحجم الكلي بالبايتات للطلب بالإضافة للترويسات ضمن الصوان.

#### حجم_البيانات (dataLen)

```
عرف حجم_البيانات: صحيح؛
```

<div dir=ltr>

```
def dataLen: int
```

</div>

الحجم الكلي بالبايتات للبيانات ضمن الصوان.

#### رمز_الحالة (statusCode)

```
عرف رمز_الحالة: صحيح؛
```

<div dir=ltr>

```
def statusCode: int
```

</div>

رمز الحالة للرد ضمن بروتوكول ب.ن.ن.ف.

#### خنق (throttle)

```
عرف خنق: صحيح؛
```

<div dir=ltr>

```
def throttle: int
```

</div>

قيمة الخنق.

#### آخر_وقت_خنق (lastThrottleTime)

```
عرف آخر_وقت_خنق: صحيح؛
```

<div dir=ltr>

```
def lastThrottleTime: int
```

</div>

آخر وقت تم إرسال فيه بيانات مخنوقة.

#### آخر_بايتات_خنق (lastThrottleBytes)

```
عرف آخر_بايتات_خنق: صحيح[64]؛
```

<div dir=ltr>

```
def lastThrottleBytes: int[64]
```

</div>

البايتات التي تم إرسالها في هذه الثانية.

#### قفل_المزامنة (mutex)

```
عرف قفل_المزامنة: صحيح[64]؛
```

<div dir=ltr>

```
def mutex: int[64]
```

</div>

يمكن استعمالها لقفل الاتصال من أجل الوصول المتزامن الآمن.


### شغل_الخادم (startServer)

```
@تصدير[mg_start]
دالة شغل_الخادم(مناديات: مؤشر[مـناديات]، بيانات_المستخدم: مؤشر، خيارات: مؤشر[مـؤشر_محارف]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، خيارات: سند[مـتم.مـصفوفة[مؤشر[مـحرف]]]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، خيارات: سند[مـتم.مـصفوفة[مؤشر[مـحرف]]]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، عدد_الخيارات: صـحيح، خيارات: ...مؤشر[محرف]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، عدد_الخيارات: صـحيح، خيارات: ...مؤشر[محرف]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، بيانات_المستخدم: مؤشر، منفذ: مؤشر[محرف]): مؤشر[سـياق]؛
دالة شغل_الخادم(منادي: مـنادى_الطلب، منفذ: مؤشر[محرف]): مؤشر[سـياق]؛
```

<div dir=ltr>

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

</div>

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

```
دالة أوقف_الخادم(سياق: مؤشر[سـياق])؛
```

<div dir=ltr>

```
@expname[mg_stop]
func stopServer(context: ptr[Context]): Void
```

</div>

تستعمل هذه الدالة لإيقاف الخادم وتحرير أي موارد كان يستعملها، تنتظر هذه الدالة
حتى يتم إنهاء كل المسالك التي يتم تنفيذها و بعدها يقوم بتحرير الموارد و إغلاق الخادم.

* `سياق` (`context`): مؤشر إلى السياق الخاص بالخادم الذي نريد إغلاقه.

### اقرأ (read)

```
دالة اقرأ(اتصال: مؤشر[اتـصال]، صوان: مؤشر، حجم_صوان: صحيح): صحيح؛
```

<div dir=ltr>

```
@expname[mg_read]
func read(connection: ptr[Connection], buffer: ptr, bufferSize: Int): Int
```

</div>

تقوم هذه الدالة بقراءة البيانات من الاتصال المشار إليه عن طريق `اتصال`.
يتم التعامل مع البيانات على أنها بيانات ثنائية و يتم تخزينها في الصوان المشار إليه من قبل `صوان`.
تُرجع عدد البايتات المقروءة عند النجاح، `0` عند إغلاق الاتصال من قبل الطرف الآخر، أو قيمة سالبة عند عدم وجود بيانات.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد قراءة البيانات منه.
* `صوان` (`buffer`): مؤشر إلى الصوان الذي نريد تخزين البيانات فيه.
* `حجم_صوان` (`bufferSize`): الحجم الأقصى بالبايتات للبيانات التي يمكن تخزينها في الصوان.

### اكتب (write)

```
دالة اكتب(اتصال: مؤشر[اتـصال]، صوان: مؤشر[مـحرف]، حجم_صوان: صـحيح): صـحيح؛
```

<div dir=ltr>

```
@expname[mg_write]
func write(connection: ptr[Connection], buffer: CharsPtr, bufferSize: Int): Int
```

</div>

تستعمل هذه الدالة لإرسال البيانات عبر اتصال ما.
تُرجع عدد البايتات المرسلة عند النجاح، أو `-1` عند الفشل.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال البيانات عبره.
* `صوان` (`buffer`): الصوان الذي يحوي البيانات التي نريد إرسالها.
* `حجم_صوان` (`bufferSize`): حجم الصوان المشار له ب `صوان` بالبايت.

### اطبع (print)

```
دالة اطبع(اتصال: مؤشر[اتـصال]، تنسيق: مؤشر[مـحرف]، أي_معطيات_أخرى...): صـحيح؛
```

<div dir=ltr>

```
@expname[mg_printf]
func print(connection: ptr[Connection], format: CharsPtr, ...any): Int
```

</div>

تستعمل هذه الدالة لإرسال رسائل منسقة عبر اتصال ما.
تُرجع عدد البايتات المرسلة عند النجاح، `0` عند إغلاق الاتصال، أو `-1` عند حدوث خطأ.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال الرسالة عبره.
* `تنسيق` (`format`): التنسيق الخاص بالرسالة المراد إرسالها.
* `أي معطيات أخرى`: المعطيات التي يحتاجها التنسيق ليشكل رسالة.

### أرسل_ملف (sendFile)

```
دالة أرسل_ملف(اتصال: مؤشر[اتـصال]، اسم_الملف: مؤشر[مـحرف]): فـارغ؛
```

<div dir=ltr>

```
@expname[mg_send_file]
func sendFile(connection: ptr[Connection], fileName: CharsPtr): Void
```

</div>

تستعمل هذه الدالة لإرسال ملف عبر اتصال ما.
تقوم هذه الدالة تلقائياً بإضافة الترويسات اللازمة.

* `اتصال` (`connection`): مؤشر إلى الاتصال المراد إرسال الملف عبره.
* `اسم_الملف` (`fileName`): اسم الملف الذي نريد إرساله.

### هات_الارتباط (getCookie)

```
دالة هات_الارتباط(
    نص_الكعكات: مؤشر[مـحرف]، اسم_الكعكة: مؤشر[مـحرف]، محتوى_الكعكة: مؤشر[مـحرف]، حجم_الكعكة: كـلمة[64]
): كـلمة[64]؛
```

<div dir=ltr>

```
@expname[mg_get_cookie]
func getCookie(cookiesString: CharsPtr, cookieName: CharsPtr, outCookieContent: CharsPtr, outCookieSize: Word[64]): Int
```

</div>

تستعمل هذه الدالة لجلب قيمة متغير معين ضمن كعكة ما.
تُرجع حجم الكعكة بالبايت عند النجاح، `-1` عند عدم إيجاد الكعكة، أو `-2` عند فشل التخزين في الصوان.

* `نص_الكعكات` (`cookiesString`): اسم الكعكة.
* `اسم_الكعكة` (`cookieName`): اسم المتغير ضمن الكعكة المشار له ب `نص_الكعكات`.
* `محتوى_الكعكة` (`outCookieContent`): الصوان الذي سيتم تخزين المحتوى ضمنه.
* `حجم_الكعكة` (`outCookieSize`): حجم الصوان المشار له ب `محتوى_الكعكة`.

### هات_الترويسة (getHeader)

```
دالة هات_الترويسة(اتصال: مؤشر[اتـصال]، اسم_الترويسة: مؤشر[مـحرف]): مؤشر[مـحرف]؛
```

<div dir=ltr>

```
@expname[mg_get_header]
func getHeader(connection: ptr[Connection], headerName: CharsPtr): CharsPtr
```

</div>

تستعمل هذه الدالة لجلب الترويسة عبر اتصال ما.
تُرجع مؤشراً إلى قيمة الترويسة أو مؤشراً إلى لا شيء عند الفشل.

* `اتصال` (`connection`): الاتصال المراد جلب الترويسة عبره.
* `اسم_الترويسة` (`headerName`): اسم الترويسة المراد جلبها.

### هات_معلومات_الطلب (getRequestInfo)

```
دالة هات_معلومات_الطلب(اتصال: مؤشر[اتـصال]): مؤشر[اتـصال]؛
```

<div dir=ltr>

```
@expname[mg_get_request_info]
func getRequestInfo(connection: ptr[Connection]): ptr[RequestInfo]
```

</div>

تستعمل هذه الدالة لجلب معلومات الطلب عبر اتصال ما.
تُرجع مؤشراً إلى معلومات الطلب.

* `اتصال` (`connection`): الاتصال المراد جلب معلومات الطلب منه.

### هات_متغير (getVariable)

```
دالة هات_متغير(
    بيانات: مؤشر[مـحرف]، حجم_البيانات: صـحيح، اسم_المتغير: مؤشر[مـحرف]، متغير_الخرج: مؤشر[مـحرف]، حجم_متغير_الخرج: صـحيح
): صـحيح؛
```

<div dir=ltr>

```
@expname[mg_get_var]
func getVariable(data: CharsPtr, dataSize: Int, variableName: CharsPtr, outVariable: CharsPtr, outVariableSize: Int): Int
```

</div>

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

</div>
