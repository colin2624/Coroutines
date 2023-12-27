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