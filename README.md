# Coroutines
coroutines

코루틴(coroutine)은 루틴의 일종
협동 루틴이라 할수있음.
코루틴의 "co"는 with또는 together를 뜻한다.

코루틴은 이전에 자신의 실행이 마지막으로 중단되었던 지점 다음의 장소에서 실행을 재개한다. 이런것을 함수의 진입점과 출구점이 여러개다. 라고 표현하기도 한다.

코루틴은 도널드 커누스에 따르면 멜빈 콘웨이는 1958년 코루틴이라는 용어를 만들어냈으며
당시 그는 이를 어셈블리 프로그램에 적용했다.

코루틴에 관해 설명된 최초의 출판물은 1963년에 등장하였다.

코루틴은 협력작업, 예외, 이벤트 루프, 반복자, 무한 목록 및 파이프와 같은 친숙한 프로그램 구성 요소를 구현하는데 적합하다.

Dream Code란?

```kotlin 
package com.survivalcoding.kotlinebasic

fun main() {
    val user = fetchUserData()
    textView.text = user.name
}
```
이게 무슨 코드냐면 서버를 한번 호출하고 서버에서 온 데이터를 텍스트뷰를 통해 텍스트로 바꿔주는 코드이다. 이것을 구현하고 싶은데 그러지못해서 드림코드라고 부른단다.

코루틴은 어싱크를 간단하게 해주는것이고, 콜백을 교체할수있다.

코루틴의 역사

2016년 4월 14일
코루틴에 대한 로드맵을 발표하면서 떡밥을 뿌렸다.

2017년 5월 18일
구글이 안드로이드 공식 언어로 코틀린 추가

2018년 10월 29일
안정화 된 버전이 배포되었다

2019년 5월 9월
공식 발표

2020년 6월 10일
우리는 코루틴을 공식 권장 사항으로 만들고 있으며
가장 많이 사용되는 3가지 jetpack 라이브러리인 Lifecycle, WorkManager 및 Room에 코루틴 지원

첫번째 예제 

```kotlin
fun main() {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello")
    Thread.sleep(2000L)
}
```
이것을 출력하면 
```
Hello
World!
```
위와 같은 값이 나오게되는데 분명 코드에선 Hello가 나중에 출력되도록 짜여있는데
왜 Hello가 먼저 출력되는지 알아보도록 하자.
여기서 중요한부분은 lanuch인데 이번 부분이 바로 코루틴 빌드라고 한다.
일명 코루틴 빌드에서 코루틴이 만들어지게 되고 그 안에서 코루틴이 실행된다.

GlobaScop를 공식 문서에 나와있는것처럼 thead로 바꿔보면

```kotlin
fun main() {
    thread {
        // delay(1000L)
        Thread.sleep(1000L)
        println("World!")
    }

    println("Hello,")
    Thread.sleep(2000L)
}
```

이렇게 작성할수있다.
여기서 delay는 일시 중단하는 서스펜드 펑션이었는데 Thread.sleep는 Thread를
블로킹하는 함수이다. 

runBlocking은 launch와 달리 자신이 호출한 스레드를 블로킹한다.

또 예를 들어

```kotlin
fun main() = runBlocking {this:CorutineScope
    GlobalScope.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    println("Hello,")
    delay(timeMillis:2000L)
}
```
위와 같은 코드를 작성하게 되면 Hello,만 출력이 되고 World!는 출력이 되지않는다
왜냐하면 메인 펑션은 2초뒤에 종료되는데 World!는 3초뒹 출력이 되기때문이다. 이것을
해결하기 위해선 job이 필요하다. 
```kotlin
fun main() = runBlocking {this:CorutineScope
    val job = GlobalScope.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    println("Hello,")
    job.join()
}
```
이렇게 작성해주면 job이 끝날때까지 기다릴수 있도록 해준다. 근데여기서 job을 여러개 
작성하게 된다면 job을 더 효율적으로 관리를 해줘야하는데 코틀린에선 이런현상을 방지하기위해 스트럭처드 컨코런시를 이용하라고 안내해준다

```kotlin
fun main() = runBlocking {this:CorutineScope
    val job = GlobalScope.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    val job1 = GlobalScope.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    println("Hello,")
    job.join()
    job1.join()
}
```
답은 간단하다 글로벌스코프가 아니라 런블로킹에서 런치를 해버리면된다.

```kotlin
fun main() = runBlocking {this:CorutineScope
    this.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    this.launch {this:CorutineScope
        delay{timeMillis:3000L}
        println("World!")
    }

    println("Hello,")
    job.join()
    job1.join()
}
```
이렇게말이다.

다음 예제는 코루틴이 굉장히 간단하다는걸 알려주는 예제이다

```kotlin
fun main() = runBlocking {
    repeat(100_000) {
        launch {
            delay(1000L)
            print(".")
        }
    }
}
```
이 예제는 10만개의 코루틴을 생성해서 1초뒤에 한번에 점을 찍게 하는코드이다. 물론
Thread로도 가능하다.

마지막 예제로 코루틴이 계속 실행이 계속되고 있다고해서 프로세스가 동작한다는게 아니라는걸 보여주는 코드이다.

```kotlin
fun main() = runBlocking {
    GlobalScope.launch {this: CoroutineScope
        repeat(times:1000) { i ->
            println("I'm sleeping $1 ...")
            delay(timeMillis:500L)
        }
    }

    delay{timeMillis:1300L}
}
```
이 예제는 천개를 1초에 두번씩 프린트하는 예제이다.

# Cancellation and Timeouts

첫번째 예제는 단순히 코루틴을 취소하는 명령어이다.

```kotlin
fun main() = runBlocking { this: CoroutineScope
    val job = launch { this: CoroutineScope
        repeat(times:1000) { i ->
            println("job: I'm sleeping $1 ...")
            delay(timeMillis:500L)
        }
    }
    delay(timeMilis:13000L)
    println(msg: "main: I'm tired of waiting!")
    job.cancel()
    job.join()
    println(msg: "main: Now I can quit.")
}

```
job에는 실행하고 있는 코루틴을 캔슬하는 기능도 있음. 
위의 코드에서 cancel()이 없다면 1000번동안 0.5초 간격으로 출력을 하게된다.
cancel이 있다면 1.3초 동안 반복을하다 코루틴을 중단하고 메인 펑션이 끝나게된다.

두번쨰 예제는 cancel이 실행되기 위하여 충족해야할 조건이 있는데 그 조건을 알아보자.

```kotlin
fun main() = runBlocking { this: CoroutineScope 
    val startTime = System.currentTimeMillis()
    val job = launch(Dispatchers.Default) {this: CoroutineScope
        var nextPrintTime = startTime
        var i = 0
        while (i < 5) {
            if (System.currentTimeMillis() >= nextPrintTime) {
                println(msg : "job : I'm sleeping ${i++} ...")
                nextPrintTime += 500L
            }
        }
    }
    delay(timeMillis: 1300L)
    println(msg: "main: I'm tired of waiting!")
    job.cancelAndJoin()
    println(msg: "main: Now I cna quit")
}
```
이 코드는 i의 값을 점점 늘려가면서 출력하되 1.3초 뒤에 코루틴이 캔슬되도록하는 코드이다.
이것을 실행하면 i의 값이 0 1 2까지만 나오게 하고 싶었지만 0 1 2 3 4까지 나와버린다.
왜냐하면 코루틴 자체가 캔슬되는데 협조적이지 않았기 때문이다. 무슨 말이냐면
첫번째 예제에는 코루틴안에 delay(timeMillis:500L) 이라는 서스펜드 펑션이 있었는데, 두번째 예제에선 연산 명령만 있고 서스펜드 펑션이 없었기 때문이다. 

여기선 delay같은 서스펜드 펑션보다 더 좋은 서스펜드 청션이 있는데 바로 yield이다.
yield를 사용하면 delay(1L)를 주지 않고도 2까지만 출력할수 있다. 

그리고 이런경우엔 코루틴이 중단되었다가 재개될때 인셉션을 던진다 그 인셉션을 보기위해
try-catch를 이용하면 볼수있다.

```kotlin
fun main() = runBlocking { this: CoroutineScope 
    val startTime = System.currentTimeMillis()
    val job = launch(Dispatchers.Default) {this: CoroutineScope
        
        try {
            var nextPrintTime = startTime
            var i = 0
            while (i < 5) {
                if (System.currentTimeMillis() >= nextPrintTime) {
                    yield( )
                    println(msg : "job : I'm sleeping ${i++} ...")
                    nextPrintTime += 500L
                }
            }   
        } catch (e: Exception) {
            kotlin.io.println("Exception [$e])
        }
    }
    
    delay(timeMillis: 1300L)
    println(msg: "main: I'm tired of waiting!")
    job.cancelAndJoin()
    println(msg: "main: Now I cna quit")
}
```
이렇게 작성하면 된다.

kotlinconf 2017에서 예제를 하나 보여준다.

```kotlin
fun posrItem(item: item) {
    val token = requestToken()
    val post = creatposr(token, item)
    processPost(post)
}
```
위의 예제는 서버에서 토큰을 불러오고 어떤 게시물을 포스트한다음 포스트 완료처리를 하는 예제이다.
위의 예제로 알 수 있는것은 CPS == callbacks이라는걸 알 수 있다.

다음 예제이다.

```kotlin
suspend fun crearePost(token: Token, item: Item) : Post {...} 
```
서스펜드 함수를 생성했더니 이 코드가 중단했다 재개를 한다는것이다.

코틀린 java식은 코드를 작성한 후에 tools - kotlin - show Kolin Bytecode - 디컴파일 

```kotlin
GlobalScope.launch {
    val userData = fetchUserData()
    var userCache = cacheUserData(userData)
    updateTextView(userCache)
}
```
when문 안에서 기준이 되는 변수의 값을 늘려가면서 순차적으로 실행하는 코드이다.
when문 이기때문에 중단하거나 재개하는것이 가능하다.

디스패처는 쿠로틴이 어떤 스레드에수 실행시킬지 결정해주는 요소이다. 예를들어

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking<Unit> {
    launch { // context of the parent, main runBlocking coroutine
        println("main runBlocking      : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Unconfined) { // not confined -- will work with main thread
        println("Unconfined            : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Default) { // will get dispatched to DefaultDispatcher
        println("Default               : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(newSingleThreadContext("MyOwnThread")) { // will get its own new thread
        println("newSingleThreadContext: I'm working in thread ${Thread.currentThread().name}")
    }
}
```

1번째 코루틴은 runblocking에서 옵셔널 파라미터 없이 실행된다.
2번째 코루틴은 main 스레드에서 실행된다
3번째 코루틴은 worker-1에서 실행된다.
4번째 코루틴은 한번 사용할떄마다 스레드를 만드는데 실제 사용할떄는 
newSingleTreadContext("MyOwnTread").use로 쓰는게 좋다.

코루틴의 실행 순서는 2번-3번-4번-1번이다.

```kotlin
import kotlinx.coroutines.*

fun log(msg: String) = println("[${Thread.currentThread().name}] $msg")

fun main() = runBlocking<Unit> {
    val a = async {
        log("I'm computing a piece of the answer")
        6
    }
    val b = async {
        log("I'm computing another piece of the answer")
        7
    }
    log("The answer is ${a.await() * b.await()}")
}
```
코틀린 라이브러리는 코루틴을 디버깅하기 위한 도구가 있는데
log라는 함수는 지금 어디서 실행되고 있는지 알려준다.
만약 어느 코루틴에서 실행되고 있는지 보고싶다면 아래 코드와 같이 작성해주면 된다.

```kotlin
import kotlinx.coroutines.*

fun log(msg: String) = println("[${Thread.currentThread().name}] $msg")

fun main() {
    newSingleThreadContext("Ctx1").use { ctx1 ->
        newSingleThreadContext("Ctx2").use { ctx2 ->
            runBlocking(ctx1) {
                log("Started in ctx1")
                withContext(ctx2) {
                    log("Working in ctx2")
                }
                log("Back to ctx1")
            }
        }
    }
}
```
withcontext를 통해서 스레드간을 점프할수 있다.

코루틴 스코프는 예제를 먼저 보고 설명하도록 하겠다.

```kotlin
import kotlinx.coroutines.*

class Activity {
    private val mainScope = CoroutineScope(Dispatchers.Default) // use Default for test purposes

    fun destroy() {
        mainScope.cancel()
    }

    fun doSomething() {
        // launch ten coroutines for a demo, each working for a different time
        repeat(10) { i ->
            mainScope.launch {
                delay((i + 1) * 200L) // variable delay 200ms, 400ms, ... etc
                println("Coroutine $i is done")
            }
        }
    }
} // class Activity ends

fun main() = runBlocking<Unit> {
    val activity = Activity()
    activity.doSomething() // run test function
    println("Launched coroutines")
    delay(500L) // delay for half a second
    println("Destroying activity!")
    activity.destroy() // cancels all coroutines
    delay(1000) // visually confirm that they don't work
}
```
안드로이드 앱을 쓰다가 앱을 종료하면 작업을 멈춰야하기때문에 코루틴 스코프가 존재한다.