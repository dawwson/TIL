# 클린 코드(Clean Code)

`#노개북` `#노마드코더` `#개발자북클럽`

- 작가 : 로버트 C. 마틴
- 출판사 : 인사이트

<br>

## Assignment #13

`2024.02.12`

### 🔖 10. 클래스

#### 기억하고 싶은 내용

[ 클래스의 체계 ]

1. 클래스를 정의하는 순서(by. 표준 자바 관례)

   1. 변수 목록

      - static public 상수 -> static private 상수 -> private 인스턴스 변수

   2. 함수 목록

      - public 함수 -> private 함수(호출하는 public 함수 바로 다음)

2. 캡슐화

   - 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 좋다.

   - 그러나 테스트 코드에서의 접근을 허용하기 위해 protected로 선언하기도 한다.

   - 캡슐화를 풀어주는 것은 언제나 최후의 수단이다.

<br>

[ 깨끗한 클래스의 기준 ]

1. 클래스는 작아야 한다.

   - 클래스의 이름에 Processor, Manager, Super와 같은 이름을 붙였다면 많은 책임을 가지고 있을 확률이 높다. **단일 책임 원칙**(SRP, 클래스나 모듈을 변경할 이유가 하나여야 한다)을 지키는 여러 개의 작은 클래스로 나누자.

   - 클래스에 속한 메서드와 변수가 서로 의존하고 있을수록 응집도가 높아진다. **일부 메서드만 사용하는 변수가 많아진다면 작은 클래스로 분리할 수 있다는 신호**다.

2. 클래스는 변경하기 쉬워야 한다.

   - 새 기능을 추가하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 것이 좋다. **이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐, 기존 코드를 변경하지 않는다**(개방-폐쇄 원칙, OCP, 클래스는 확장에 개방적이고 수정에 폐쇄적이어야 한다).

   - 변경으로부터 격리하는 방법

     - **인터페이스와 추상 클래스**를 사용하여 클라이언트 클래스가 상세한 구현에 의존하지 않도록 한다. 상세한 구현에 의존하면 테스트가 어렵다.

     - 인터페이스를 사용하면 구체 클래스를 모킹하는 테스트용 클래스를 만들 수 있다. 이렇게 테스트가 가능해지면 클래스 간 결합도가 낮아진다.

     - 결과적으로 의존 역전 법칙(DIP, 클래스는 상세한 구현이 아니라 추상화에 의존해야 한다)을 따르는 클래스가 나온다.

     - JS 예시)

       ```js
       class InventoryTracker {
         constructor(items, requester) {
           this.items = items;
           this.requester = requester;
         }

         requestItems() {
           this.items.forEach((item) => {
             this.requester.requestItem(item);
           });
         }
       }

       class InventoryRequesterV1 {
         constructor() {
           this.REQ_METHODS = ["HTTP"];
         }

         requestItem(item) {
           // ...
         }
       }

       class InventoryRequesterV2 {
         constructor() {
           this.REQ_METHODS = ["WS"];
         }

         requestItem(item) {
           // ...
         }
       }

       // 의존성을 외부에서 만들어 주입해줌으로써,
       // 요청 모듈을 새롭게 만든 웹소켓 사용 모듈로 쉽게 바꿔 끼울 수 있게 되었다.
       const inventoryTracker = new InventoryTracker(
         ["apples", "bananas"],
         new InventoryRequesterV2()
       );
       inventoryTracker.requestItems();
       ```

#### 오늘 읽은 소감

- 처음에 인터페이스 설계를 잘 하는 것도 중요하겠다는 생각이 들었다. OCP와 DIP를 위해 인터페이스를 만들어도, 인터페이스가 자주 변경된다면 구체 클래스와 클라이언트 클래스도 변경되는 것이 아닌가...?

- [JS로 보는 클린코드 문서](https://github.com/qkraudghgh/clean-code-javascript-ko)를 보면서 한 가지 느낀 것이 있다. JS에는 인터페이스가 없기 때문에 다른 객체나 클래스에 노출되는 메서드와 속성으로 암시적인 추상화를 하게 된다. JS만으로도 객체 지향 프로그래밍을 할 수 있다고 알고 있다. 다만 TS를 적용하면 SOLID 원칙을 좀 더 명시적으로 지킬 수 있는 장점이 있는 것 같다는 생각이 들었다.

#### 궁금한 내용 & 잘 이해되지 않는 내용

- JS로 보는 SOLID 원칙
  - https://github.com/qkraudghgh/clean-code-javascript-ko?tab=readme-ov-file#solid
