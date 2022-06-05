### volatile 키워드

volatile 는 쓰레드들에 대한 변수의 가시성을 보장한다. 멀티스레드에서 어플리케이션에 non-volatile 변수에 대한 작업은 성능상의 이유로 cpu 캐시를 이용한다. 둘 이상의 cpu가 탑재된 장비에서 어플리케이션을 실행한다면 각 쓰레드들을 메인 메모리의 변수를 각 cpu 의 캐시로 복사하여 읽어드린다.

가시성 문제 쓰레드가 변경한 값이 메인 메모리에 저장되지 않아서 다른 쓰레드가 이값을 볼 수 없는 상황을 가시성 문제라고 한다.

만약 공유변수가 있다고 가정해보자 volatile 키워드를 선언하면 이변수에대한 쓰기 작업은 즉각 메인 메모리로 이루어질 것이고 읽기 작업또한 메인 메모리로 다이렉트로 이루어 지게된다.
즉 volatile 선언은 다른 쓰레드에 쓰기 작업에 대한 가시성을 보장한다.

volatile 의 특징
한 쓰레드가 volatile 변수를 수정할 때, 단지 이 volatile 변수만이 메인 메모리로 저장되는 것이 아니라, 이 쓰레드가 volatile 변수를 수정하기 전에 수정한 모든 변수들이 함께 메인 메모리에 저장(flushed)된다. 그리고 쓰레드가 volatile 변수를 메인 메모리에서 읽어들일 때, volatile 변수를 수정하면서 메인 메모리로 함께 저장된 다른 모든 변수들도 메인 메모리로부터 함께 읽어들여진다.

volatile 키워드는 "happends before guarantee" 성질을 갖는데, 이것은 volatile 변수에 대한 읽기/쓰기 명령은 JVM 에 의해 재정리되지 않음을 보장한다는 의미이다. volatile 변수에 대한 읽기/쓰기 명령을 기준으로, 이 변수 전에 존재하는 다른 명령들은 자기들끼리 얼마든지 재정리 될 수 있다. 그리고 이 변수 뒤에 존재하는 다른 명령들 또한 자기들끼리 재정리 될 수 있다.

아래 예시를 확인해보
volatile 변수인 sharedObject.volatile 를 기준으로
nonVolatile1,2,3 의 코드 순서는 jvm 에 의해서 재정리 될 수 있다.
마찬가지로 nonVolatile4,5,6 코드도 재정리(코드의 실행순서가 변경)될 수 있다.
하지만 (nonVolatile1,2,3(2,1,3 순서이든)) =>
sharedObject.volatile => (nonVolatile4,5,6) 순서는 보장된다.

```
sharedObject.nonVolatile1 = 123;
sharedObject.nonVolatile2 = 456;
sharedObject.nonVolatile3 = 789;

sharedObject.volatile     = true; //a volatile variable

int someValue1 = sharedObject.nonVolatile4;
int someValue2 = sharedObject.nonVolatile5;
int someValue3 = sharedObject.nonVolatile6;
```

### 한계

volatile 키워드는 하나의 스레드가 공유변수를 수정하고 다른 하나의 스레드가 해당 변수를 읽기만한다면 유효하다. 하지만 동시에 여러스레드가 공유변수를 수정시에는 데이터 정합성을 보장하지 않기 때문에 syncronized 키워드를 활용해서 임계영역을 컨트롤 해야한다. volatile 는 변수에 접근하는 스레드를 블록시키지 않기 때문이다.

### 정리

volatile 변수의 읽기/쓰기는 메인 메모리를 이용한다. 메인 메모리로부터 데이터를 읽고 쓰는 작업은 CPU 캐시를 이용하는 것 보다 많은 비용이 요구된다. 또한 volatile 선언은 JVM 의 성능 향상을 위한 기술인, 코드 재정리를 막기도 한다. 그러므로 volatile 키워드는 변수의 가시성 보장이 반드시 필요한 경우에만 사용되어야 한다.

참고: https://parkcheolu.tistory.com/16
