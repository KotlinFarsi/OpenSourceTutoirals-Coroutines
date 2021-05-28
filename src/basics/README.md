<div dir="rtl">


## اصول اولیه کروتین

در این بخش برای آشنایی اولیه, چند مثال آورده شده است.



## مطالبی که در این فایل شامل می‌شوند:

* [اولین کروتین را اجرا کنید](#%D8%A7%D9%88%D9%84%DB%8C%D9%86-%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86-%D8%B1%D8%A7-%D8%A7%D8%AC%D8%B1%D8%A7-%DA%A9%D9%86%DB%8C%D8%AF)

* [ایجاد ارتباط بین کروتین بلاک کننده و غیربلاک کننده](#%D8%A7%DB%8C%D8%AC%D8%A7%D8%AF-%D8%A7%D8%B1%D8%AA%D8%A8%D8%A7%D8%B7-%D8%A8%DB%8C%D9%86-%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86-%D8%A8%D9%84%D8%A7%DA%A9-%DA%A9%D9%86%D9%86%D8%AF%D9%87-%D9%88-%D8%BA%DB%8C%D8%B1%D8%A8%D9%84%D8%A7%DA%A9-%DA%A9%D9%86%D9%86%D8%AF%D9%87)

* [منتظر ماندن برای تمام شدن یک Job](#%D9%85%D9%86%D8%AA%D8%B8%D8%B1-%D9%85%D8%A7%D9%86%D8%AF%D9%86-%D8%A8%D8%B1%D8%A7%DB%8C-%D8%AA%D9%85%D8%A7%D9%85-%D8%B4%D8%AF%D9%86-%DB%8C%DA%A9-job)

* [برنامه نویسی همزمان به شکل ساختار یافته](#%D8%A8%D8%B1%D9%86%D8%A7%D9%85%D9%87-%D9%86%D9%88%DB%8C%D8%B3%DB%8C-%D9%87%D9%85%D8%B2%D9%85%D8%A7%D9%86-%D8%A8%D9%87-%D8%B4%DA%A9%D9%84-%D8%B3%D8%A7%D8%AE%D8%AA%D8%A7%D8%B1-%DB%8C%D8%A7%D9%81%D8%AA%D9%87)

* [سازنده اسکوپ](#%D8%B3%D8%A7%D8%B2%D9%86%D8%AF%D9%87-%D8%A7%D8%B3%DA%A9%D9%88%D9%BE)

* [ریفکتور کردن به suspend فانکشن](#%D8%B1%DB%8C%D9%81%DA%A9%D8%AA%D9%88%D8%B1-%DA%A9%D8%B1%D8%AF%D9%86-%D8%A8%D9%87-suspend-%D9%81%D8%A7%D9%86%DA%A9%D8%B4%D9%86)

*  [کروتین‌ها سبک وزن هستند](#%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86%D9%87%D8%A7-%D8%B3%D8%A8%DA%A9-%D9%88%D8%B2%D9%86-%D9%87%D8%B3%D8%AA%D9%86%D8%AF)

* [کروتین‌های سراسری شبیه به تردهای شبح هستند](#%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86%D9%87%D8%A7%DB%8C-%D8%B3%D8%B1%D8%A7%D8%B3%D8%B1%DB%8C-%D8%B4%D8%A8%DB%8C%D9%87-%D8%A8%D9%87-%D8%AA%D8%B1%D8%AF%D9%87%D8%A7%DB%8C-%D8%B4%D8%A8%D8%AD-%D9%87%D8%B3%D8%AA%D9%86%D8%AF)




#### اولین کروتین را اجرا کنید

</div>

```kotlin
import kotlinx.coroutines.*

fun main() {
    GlobalScope.launch { // launch a new coroutine in background and continue
        delay(1000L) // non-blocking delay for 1 second (default time unit is ms)
        println("World!") // print after delay
    }
    println("Hello,") // main thread continues while coroutine is delayed
    Thread.sleep(2000L) // block main thread for 2 seconds to keep JVM alive
}
```


<div dir="rtl">

پس از اجرا نتیجه کد به شکل زیر خواهد بود:


</div>

```text
Hello,
World!
```
<div dir="rtl">

کروتین‌ها تردهای سبک وزنی هستند که با متدهای سازنده کروتین ساخته می‌شوند.

به عنوان مثال در کد بالا متد `launch` یک سازنده کروتین هست که در کانتکست `GlobalScope` اجرا شده است, به این معنی که تا زمانی که چرخه حیات این کروتین به چرخه حیات اپلیکیشن محدود شده است



> منظور از کروتین بلاک کننده این است که تا زمانی که این کروتین درحال اجرا است, ترد اصلی اپلیکیشن بلاک می‌شود.

#### ایجاد ارتباط بین کروتین بلاک کننده و غیربلاک کننده

در مثال بالا ما ترکیبی از غیربلاک کننده `delay(...)` و بلاک کننده `Thread.sleep(...)` را داشتیم. در این حالت پیدا کردن کد بلاک کننده از از غیربلاک کننده مقداری سخت است و برای ساده تر شدن از سازنده کروتینی به اسم  [runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html) استفاده می‌کنیم.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() {
    GlobalScope.launch { // launch a new coroutine in background and continue
        delay(1000L)
        println("World!")
    }
    println("Hello,") // main thread continues here immediately
    runBlocking {     // but this expression blocks the main thread
        delay(2000L)  // ... while we delay for 2 seconds to keep JVM alive
    }
}
```

<div dir="rtl">

تنیجه این کد یکسان است اما در این کد از تابع [delay](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/delay.html) که غیربلاک کننده است استفاده کرده‌ایم.در اینجا ترد اصلی اپلیکیشن,کروتین `runBlocking` را اجرا میکند و تا زمانی این کروتین به پایان برسد, منتظر می‌ماند.

این کد می‌تواند به شکل دیگری هم بازنویسی شود, که در این شکل همه کدها در داخل کروتین `runBlocking` قرار می‌گیرند.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> { // start main coroutine
    GlobalScope.launch { // launch a new coroutine in background and continue
        delay(1000L)
        println("World!")
    }
    println("Hello,") // main coroutine continues here immediately
    delay(2000L)      // delaying for 2 seconds to keep JVM alive
}
```

<div dir="rtl">

اغلب اوقات `runBlocking<Unit> { ... }` برای نوشتن یونیت تست کاربرد دارد.

</div>

```kotlin
class MyTest {
    @Test
    fun testMySuspendingFunction() = runBlocking<Unit> {
        // here we can use suspending functions using any assertion style that we like
    }
}
```
<div dir="rtl">

#### منتظر ماندن برای تمام شدن یک Job

کروتین امکانی را می‌دهد تا بدون این که تاخیری در برنامه ایجاد کنیم, متنظر بمانیم تا اجرای آن کروتین به اتمام برسد.

در این روش به طور صریح و بدون بلاک شدن به کروتین از طریق متد `join` اعلان میکنیم که تا تمام شدن این کروتین منتظر بماند.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
//sampleStart
    val job = GlobalScope.launch { // launch a new coroutine and keep a reference to its Job
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    job.join() // wait until child coroutine completes
//sampleEnd
}
```

<div dir="rtl">
در کد بالا کروتین اصلی به هیچ وجه به تسکی که در بک‌گراند اجرا می‌شود کاری ندارد.


#### برنامه نویسی همزمان به شکل ساختار یافته

برای استفاده از کروتین مواردی هست که می‌بایست درنظر گرفته شود:
زمانی که از سازنده کروتین `GlobalScope.launch` استفاده می‌کنیم یعنی یک کروتین سطح بالا را ایجاد کرده‌ایم, انجام چنین کاری درحالی که سبک وزن است و مادامی که رفرنس این کروتین وجود داشته باشد حافظه مصرف می‌شود و اگر رفرنس این کروتین فراموش شود, این کروتین درحال اجرا خواهد ماند. اگر یک کروتین با یک تاخیر طولانی هنگ کند, دراین حالت اگر کروتین های زیادی اجر شوند و همچنین از متد [join](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html) آنها استفاده شود, باعث ایجاد خطا در برنامه می‌شود.

راه حل بهتر اسفاده از برنامه نویسی همزمان به شکل ساختار یافته است, به این شکل که, به جای استفاده از [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) , ازاسکوپ های مختص به هر کار استفاده کنیم به عبارت دیگر بازه عملکرد کروتین را محدود به یک بخش کوچک‌تری از اپلیکیشن کنیم.

در مثال بالا یک کروتین `runBlocking` کل فانکشن `main` را شامل می‌شود. که این کروتین یک نمونه از `GlobalScope` را در بلاک خود دارد, در این بلاک ما می‌توانیم کروتین هایی اجرا کنیم که نیاز به استفاده از متد `join `را ندارند, به این خاطر که تا زمانی که کروتین های داخلی کروتین (`runBlocking`) تمام نشو‌‌ند این کروتین درحال اجرا خواهد ماند.

در این حالت مثال بالا به شکل بهتری قابل پیاده سازی است:
</div>


```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking { // this: CoroutineScope
    launch { // launch a new coroutine in the scope of runBlocking
        delay(1000L)
        println("World!")
    }
    println("Hello,")
}
```
<div dir="rtl">

#### سازنده اسکوپ

علاوه بر این که اسکوپ ها از طریق سازنده‌های کروتین قابل استفاده هستند,یک سازنده [coroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html) هم وجود دارد که وظیفه آن به طور اختصاصی ایجاد اسکوپ است که در موارد داخلی قابل استفاده است.مشابه همانطور که در بالا اشاره شد تا زمانی که کروتین های داخلی آن اسکوپ تمام نشوند این کروتین اسکوپ درحال اجرا خواهد ماند. تفاوت اصلی `runBlocking`  و  `coroutineScope` این است که `coroutineScope` تا زمانی که همه کروتین های داخلی تمان نشده‌اند, باعث بلاک شدن ترد جاری نمی‌شود.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking { // this: CoroutineScope
    launch {
        delay(200L)
        println("Task from runBlocking")
    }

    coroutineScope { // Creates a coroutine scope
        launch {
            delay(500L)
            println("Task from nested launch")
        }

        delay(100L)
        println("Task from coroutine scope") // This line will be printed before the nested launch
    }

    println("Coroutine scope is over") // This line is not printed until the nested launch completes
}
```

<div dir="rtl">

#### ریفکتور کردن به suspend فانکشن‌
در مثال قبل, برای ریفکتور کردن کد میتوانیم بلاک `launch` را داخل یک فانکشن قرار دهیم و بخاطر این که این متد در سازنده کروتین قابل استفاده باشد, میبایست برای فانکشن کلیدواژه `suspend` را قرار دهیم, قابلیت دیگر `suspend` فانکشن این است که میتواند در یک `suspend` فانکشن دیگر قابل استفاده باشد. مثل فانکشن delay که در مثال زیر آورده شده است.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch { doWorld() }
    println("Hello,")
}

// this is your first suspending function
suspend fun doWorld() {
    delay(1000L)
    println("World!")
}
```

<div dir="rtl">


اما اگر فانکشن ریفکتور شده دارای یک `CoroutineScope`  باشد و داخل یک اسکوپ دیگر صدا زده شود چه می‌شود؟

یکی از راه‌حل ها تبدیل این فانکشن به یک اکسنشن فانکشن روی `CoroutineScope` است اما این روش شاید زیاد کاربردی نباشد. راه حل بهتر میتواند داشتن یک پارامتر `CoroutineScope` در داخل یک کلاس به طور مشخص باشد یا این که میتواند به صورت ضمنی باشد (وقتی که کلاس بیرونی `CoroutineScope` را پیداه سازی کرد).

آخرین روش [CoroutineScope(coroutineContext)](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope.html) می‌تواند استفاده شود که این هم به لحاظ ساختاری, ناامن است, به این دلیل شما کنترلی بر اسکوپ اجرایی این متد ندارید.



#### کروتین‌ها سبک وزن هستند

کد زیر را اجرا کنید:

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    repeat(100_000) { // launch a lot of coroutines
        launch {
            delay(1000L)
            print(".")
        }
    }
}
```

<div dir="rtl">
این کد صدهزار کروتین را اجرا می‌کند که هر کروتین یک نقطه را چاپ می‌کند. اگر این کار را با ترد‌ها انجام دهیم به احتمال زیاد خطای کمبود حافظه اتفاق می‌افتد.



#### کروتین‌های سراسری شبیه به تردهای شبح هستند

کد زیر یک کروتین طولانی مدت را در [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/index.html) اجرا می‌کند که عبارت "I'm sleeping" را هر ثانیه دوبار چاپ می‌کند و بعد از یک تاخیر به فانکشن اصلی بازمیگردد.
</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
//sampleStart
    GlobalScope.launch {
        repeat(1000) { i ->
            println("I'm sleeping $i ...")
            delay(500L)
        }
    }
    delay(1300L) // just quit after delay
//sampleEnd
}
```
<div dir="rtl">
</div>
