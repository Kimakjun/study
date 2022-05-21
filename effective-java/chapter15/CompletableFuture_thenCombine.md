## CompletableFuture_thenCombine

효과적으로 두개의 연산 합 구하기

- a, b 라는 연산이 존재, 이 두 연산은 어느정도 처리 시간이 존재
- a, b 두개의 연산을 실행하고 각각의 결과의 합을 구하는 연산을 효율적으로 처리하는 것이 필요

```
ExcutorService excutorService = ExcutorService.newFixedThreadPool(10)
int x = 1000

CompletableFuture<Integer> a = new CompletableFuture<>()
CompletableFuture<Integer> b = new CompletableFuture<>()
CompletableFuture<Integer> c = a.thenCombine(b, (y, z) -> y + z)
excutorService.submit(() -> a.complete(f(x)))
excutorService.submit(() -> b.complete(g(x)))

result = c.get()
excutorService.shutdown()

```

위와 같이 연산을 실행시켰을때 두개의 연산을 합치는 c 연산은 다른 두작업이 종료 시점까지 스레드에서 실행되지 않는다. a, b 연산을 끝날때까지 기다리는 블록문제가 발생하지 않는다.
그러므로 병렬 실행의 효율성은 높히고 데드락은 피할 수 있는 방법이다.

만약 아래와 같이 thenCombine 연산을 사용하지 않고 단순히 future 의 결과를 get 해서 기다렸다면 블록이 발생하게 된다.

```
CompletableFuture<Integer> a = new CompletableFuture<>()
CompletableFuture<Integer> b = new CompletableFuture<>()

excutorService.submit(() -> a.complete(f(x)))
excutorService.submit(() -> b.complete(g(x)))

result = a.get() + b.get() // a, b 중 먼저 끝난 연산이 다른 연산을 기다리며 블록

```
