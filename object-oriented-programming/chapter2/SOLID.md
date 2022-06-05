### 1. 단일 책임 원칙

클래스는 단한개의 책임을 가져야 한다.

1.1 단일 책임 원칙 위반이 불러오는 문제점

```
public class DataViewer {

  void display(){
    String data = loadHtml()
    updateGui(data)
  }

  String loadHtml(){
    // rest call to get html
    return htmlData
  }

  void updateGui(String data){
    // parsingData(data)
    // show ui with data
  }

  GuiData parsingData(String data){

  }

}
```

만약 위와 같은 구조에서 loadHtml 반환 값이 string 이 아니라 byte 로 변환된다면 DataViewer 는 updateGui 의 data 파라미터 구조도 변화되어야하고 parsingData 의 파싱 데이터 타입도 변화 되어야한다. 이는 데이터를 읽는 책임과 데이터를 화면에 보여주는 책임이 한클래스에 밀접하게 결합되어 있어서 발생하는 문제이다.

하지만 각각의 단일책임 원칙을 지키면서 초기 부터 설계하는 것은 쉽지 않다. 좋은 출발점은 메서드를 실행하는 것이 누구 인지 확인해 보는 방법이다.

```
GuiApplication ---> DataViewer(+display(), +loadData())

DataProcessor ---> DataViewer(+display(), +loadData())
```

위 예시에서 DataViewer 는 display, loadData 함수를 제공한다.
DataViewer 의 display 는 GuiApplication 사용하고 DataProcessor는 loadData 를 사용한다. 이와 같이 클래스의 사용자들이 서로 다른 메서드를 사용한다면 그들의 메서드는 각각 다른 책임에 속할 가능성이 높고 책임 분리 후보가 될 수 있다.

### 개방 폐쇄 원칙

- 확장에는 열려 있어야하고, 변경에는 닫혀 있어야한다.
  - 기능을 변경하거나 확장 할 수 있어야하고
  - 그 기능을 사용하는 코드는 수정하지 않는다.

개방 폐쇠 원칙을 구현하는 방법에는 인터페이스를 선언하며 변화되는 부분(확장)을 추상화하는 방법이다.

또다른 방법은 상속을 이용하는 것이다.
만약 다음과 같은 클라이언트의 요청이 왔을 경우 http 응답 프로토콜에 맞춰 데이터를 전송해주는 ResponseSender 가 있다고 하자

```
public class ResponseSender {
  private Data data;
  // 생성자..

void send(){
  sendHeader()
  sendBody()
}

protected void sendHeader(){

}

protected void sendBody(){

}

}
```

만약 데이터를 압축해서 전송하는 기능을 추가하고 싶으면 아래 예시와 같이 ResponseSender 클래스를 상속받아 sendBody 에 내용을 오버라이딩 하면된다. 그렇게 되면 ZippedResponseSender 클래스는 기존 기능에 압축기능을 추가하는 반면에(확장) 기존 ResponseSender 의 코드는 변경되지 않았다(변경) 즉 확장에는 열려있고 변경에는 닫혀있는 코드이다.

```
public class ZippedResponseSender extends ResponseSender {


  @Override
  protected void sendBody(){

  }

}

```

사실 개방폐쇄의 원칙을 지키기 위해서는 변화가 예상되는 것을 추상화해야한다. 모든 것을 예상하지는 못하더라도 코드를 작성할때, 변화 요구가 발생하면 관련된 구현을 추상화해서 개방 폐쇄 원칙에 맞게 수정할 수 있는지 확인하는 습관을 가져야한다.

### 리스코프 치환 원칙

- 상위 타입의 객체를 하위타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 작동해야한다.

리코스프 치환 원칙을 지키기 위해서는 하위타입은 상위 타입에서 정의한 명세를 벗어나지 않는 범위에서 구현해야한다.

리스코프 치환 원칙을 지키지 못하는 경우

```
class Coupon {
  int calcDisCountAmount(Item item){
    if(item instanceof Specialltem){
      return 0 // LSP 원칙 위반
    }
    return item.getPrice() * discountRate;
  }
}
```

리스코프 원칙 지키도록 수정

```
class Item {
  boolean isDisCountAvailable(){
    return true // 변경 가능한 부분을 상위 타입에 추가
  }
}

class OtherItem extends Item {
  @Override
  boolean isDisCountAvailable(){
    return true // 변경 가능한 부분을 상위 타입에 추가
  }
}

class Coupon {
  int calcDisCountAmount(Item item){
    if(!item.isDisCountAvailable){ instanceof 연산자 제거
      return 0
    }
    return item.getPrice() * discountRate;
  }
}

```

### 인터페이스 분리 원칙

- 인터페이스는 그 인터페이스를 사용하는 클라이언트 기준으로 분리해야한다.

### 의존 역전 원칙

- 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안된다. 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야한다.

위 정의를 쉽게 말하자면 고수준 모듈은 어떤 의미있는 단일 기능을 제공하는 모듈이고, 저수준 모듈은 고수준 모듈의 기능을 구현하기 위해 필요한 하위 기능의 실제 구현으로 정의할 수 있다.

### 정리

단일 책임 원칙과 인터페이스 분리 원칙은 객체가 커지지 않도록 막아준다.
리스코프 치환 원칙과 의존 역전 원칙은 개방 폐쇄 원칙을 지원한다. 개방 폐쇄 원칙은 변화하는 부분을 추상화 하고 다형성을 이용함으로써 기능 확장을 하면서도 기존 코드를 수정하지 않도록 만들어 준다. 여기서 변화되는 부분을 추상화 할 수 있도록 도와주는 원칙이 의존 역전 원칙이고 다형성을 도와주는 원칙이 리스코프 치환 원칙이다.
