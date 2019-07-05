<div dir="rtl">


## اصول اولیه کروتین

در این بخش برای آشنایی اولیه, چند مثال آورده شده است.



## مطالبی که در این فایل شامل می‌شوند:

* [اولین کروتین را اجرا کنید](#%D8%A7%D9%88%D9%84%DB%8C%D9%86-%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86-%D8%AE%D9%88%D8%AF%D8%AA%D8%A7%D9%86-%D8%B1%D8%A7-%D8%A7%D8%AC%D8%B1%D8%A7-%DA%A9%D9%86%DB%8C%D8%AF)

* [ایجاد ارتباط بین کروتین بلاک کننده و غیربلاک کننده](#%D8%A7%DB%8C%D8%AC%D8%A7%D8%AF-%D8%A7%D8%B1%D8%AA%D8%A8%D8%A7%D8%B7-%D8%A8%DB%8C%D9%86-%DA%A9%D8%B1%D9%88%D8%AA%DB%8C%D9%86-%D8%A8%D9%84%D8%A7%DA%A9-%DA%A9%D9%86%D9%86%D8%AF%D9%87-%D9%88-%D8%BA%DB%8C%D8%B1%D8%A8%D9%84%D8%A7%DA%A9-%DA%A9%D9%86%D9%86%D8%AF%D9%87)



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
