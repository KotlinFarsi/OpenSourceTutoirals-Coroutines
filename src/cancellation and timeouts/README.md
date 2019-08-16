<div dir="rtl">
    
## لغوکردن و پایان زمان اجرا



## مطالبی که در این فایل شامل می‌شوند:

- لغو کروتین
- لغو مشارکتی
- بررسی وضعیت لغو شدن
- بستن منابع با استفاده از ‍`finaly`
- اجرای کروتین با قابلیت لغو نشدن
- پایان زمان اجرا



#### لغو کروتین

در اپلیکیشن‌هایی که برای مدت طولانی‌تر درحال اجرا هستند, بهتر است که کنترل بیشتری بر روی کروتین داشته باشیم, به عنوان مثال وقتی از صفحه ای که یک کروتین درحال اجرا است خارج شویم, کروتین درحال اجرا می‌ماند و پاسخ کروتین بدون استفاده می‌ماند, برای این حالت,با اسفاده می‌توانیم با استفاده از [Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html) یی که کروتین بیلدر [launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html)  به ما می‌دهد, کروتین را لغو کنیم.

</div>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
//sampleStart
    val job = launch {
        repeat(1000) { i ->
            println("job: I'm sleeping $i ...")
            delay(500L)
        }
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancel() // cancels the job
    job.join() // waits for job's completion 
    println("main: Now I can quit.")
//sampleEnd    
}
```

<div dir="rtl">
    
خروجی کد به شکل زیر خواهد بود:

</div>

```
job: I'm sleeping 0 ...
job: I'm sleeping 1 ...
job: I'm sleeping 2 ...
main: I'm tired of waiting!
main: Now I can quit.
```

<div dir="rtl">
    
    
برای لغو کردن کروتین متد  [cancelAndJoin](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/cancel-and-join.html) هم وجود دارد که ترکیبی از  [cancel](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/cancel.html) و [join](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/join.html) است.


</div>

