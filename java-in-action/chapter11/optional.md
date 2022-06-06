### optional

#### Optional 적용 패턴

빈 Optional

```
Optional<Car> optCar = Optional.empty()
```

null 이 아닌값으로 Optional 만들기

```
Optional<Car> optCar = Optional.of(car) // car 가 만약 null 이라면 즉시 null 예외 발생.
```

null 값으로 Optional 만들기

```
Optional<Car> optCar = Optional.ofNullable(car)
```

map, flatMap
Optional 객체에 Optional 객체를 리턴하는 함수를 호출하면 Optional 로 두번 싸이게된다.
이를 해결하기 위해서 flatMap 를 사용하여 중접되지 않게 구현할 수 있다.

```
Optional<Person> optPerson = Optional.of(person)
optPerson.flatMap(Person::getCar)   // getCar, getInsurance 모두 optional 를 리턴한다고 가정
         .flatMap(Car::getInsurance)
         .map(Insurance::getName)
         .orElse("Unknown");
```

#### 도메인 모델에 Optional 사용시 데이터 직렬화할 수 없는 이유

Optional 클래스는 필드 형식으로 사용할 것을 가정하지 않았으므로 직렬화 인터페이스를 구현하지 않는다.
따라서 Optional 사용한다면 직렬화 모델을 사용하는 도구나 프레임워크에 문제가 생길 수 있다. 이와 같은 단점에서
Optional 클래스를 사용해서 도메인 모델을 구성하는 것은 바람직하다. 그렇기 때문에 아래 예시처럼 Optional 로 값을
반환 받을 수 있는 메서드를 추가하는 방식을 사용할 수 있다.

```
public class Person{
  private Car car;
  public Optional<Car> getCarAsOptional(){
    return Optional.ofNullable(car)
  }
}
```

### Optional Stream 조작

...
