# 클린 코드(Clean Code)

`#노개북` `#노마드코더` `#개발자북클럽`

- 작가 : 로버트 C. 마틴
- 출판사 : 인사이트

<br>

## Assignment #08

`2024.02.04`

### 🔖 6. 객체와 자료 구조

#### 기억하고 싶은 내용

[ 자료 추상화 ]

- 객체는 자료를 숨기고 함수를 공개한다.
- 멤버 변수마다 getter, setter만 정의했다고 해서 내부 구현이 감춰지지 않는다.
- 핵심은 추상 인터페이스를 제공해서 사용자가 구현을 모른 채 자료의 핵심을 조작하는 것이다.

[ 자료 구조와 객체의 차이 ]

1. 자료 구조

   - 자료를 그대로 공개하며, 함수를 제공하지 않는다.

     ```java
     public class Square {
        public Point topLeft;
        public double side;
     }

     public class Rectangle {
        public Point topLeft;
        public double height;
        public double width;
     }

     public class Circle {
        public Point center;
        public double radius;
     }

     public class Geometry {
        public final double PI = 3.14;

        public double area(Object shape) throws NoSuchShapeException {
           if (shape instanceof Square) {
              Square s = (Square) shape;
              return s.side * s.side;
           } else if (shape instanceof Rectangle) {
              Rectangle r = (Rectangle)shape;
              return r.height * r.width;
           } else if (shape instanceof Circle) {
              Circle c = (Circle)shape;
              return PI * c.radius * c.radius;
           }
           throw new NoSuchException();
        }
     }
     ```

2. 객체

   - 추상화 뒤로 자료를 숨기고, 자료를 다루는 함수만 공개한다.

     ```java
     public class Square implements Shape {
        private Point topLeft;
        private double side;

        public double area() {
           return side * side;
        }
     }

     public class Rectangle implements Shape {
        private Point topLeft;
        private double height;
        private double width;

        public double area() {
           return height * width;
        }
     }

     public class Rectangle implements Shape {
        private Point center;
        private double radius;
        public final double PI = 3.14;

        public double area() {
           return PI * radius * radius
        }
     }
     ```

3. 둘은 상호 보완적이다.
   - 절차적인 코드는 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다.
   - 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
   - 항상 객체가 정답인 것은 아니다.

[ 디미터 법칙 ]

1. 개념

   - 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다.
   - 객체는 조회 함수로 내부 구조를 공개하면 안 된다는 의미.
   - 클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다.
     - f가 생성한 객체
     - f 인수로 넘어온 객체
     - C 인스턴스 변수에 저장된 객체
   - 위 객체에서 허용된 메서드가 반환하는 객체의 메서드는 호출하면 안 된다.

2. 추이적 탐색 피하기 `[p.395 참고]`

   - A가 B를 사용하고 B가 C를 사용해도 A가 C를 알 필요는 없다.
   - 자신이 직접 사용하는 모듈만 알야야 한다.

     - 내가 사용하는 모듈이 내게 필요한 서비스를 모두 제공해야 한다.
     - 그렇지 않으면 설계를 바꿀 때 B와 C 사이에 다른 모듈을 넣기가 쉽지 않다.

       ```js
       // Bad
       a.getB().getC().doSomething();

       // Good
       // 1
       myCollaborator.doSomething();
       // 2
       a.b.c.doSomething();
       ```

3. 혼합된 구조를 피하기

   - 자료 구조 접근 방식이면서 함수가 있다던지, 객체 접근 방식이면서 공개 변수나 공개 getter, setter가 있다던지... 이런 구조는 피하자.

4. 구조체 감추기

   - 객체는 '무언가를 하라'고 말해야 한다. 내부 구조를 드러내면 안 된다.
   - 함수는 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다.

5. 자료 전달 객체

   - 공개 변수만 있고 함수가 없는 클래스를 자료 전달 객체(Data Transfer Object, DTO)라고도 한다.
   - 데이터베이스에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 단계에서 흔히 사용한다.
   - 활성 레코드(Active Record)
     - DTO의 특수한 형태. 공개 변수가 있거나, 비공개 변수에 getter/setter가 있는 자료 구조 + save, find 같은 함수도 제공한다. => 혼합된 구조
     - 활성 레코드에 비즈니스 규칙 메서드를 추가해, 자료 구조를 객체로 취급하는 것은 바람직하지 않다.
     - 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

[ 결론 ]

1. 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 더 적합하다.
2. 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다.

#### 오늘 읽은 소감

- Java와 같은 언어로는 무조건 객체지향 프로그래밍으로 개발해야 하는 줄 알았는데, 자료 구조와 절차적 코드로 설명한 예시를 보면서 새로운 시각으로 접근할 수 있었다. 그리고 절차적 프로그래밍과 객체지향 프로그래밍이 완전한 대척점에 있는 것이 아니라는 것, 상호 보완적이라는 것을 알게 되었다.
- 활성 레코드에 대한 내용에서 "비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다."는 말이 잘 이해가 가지 않는다. 그동안 DTO 클래스에서 신나게 비즈니스 규칙 메서드를 만들었던 거 같은데...😿 어떻게 개선할 수 있을지 예시를 한 번 찾아봐야겠다.

#### 궁금한 내용 & 잘 이해되지 않는 내용

1. 구현을 왜 감춰야 하는가?
   - 객체지향 프로그래밍에서는 '캡슐화'라고 한다.
   - 데이터에 대한 직접적인 접근을 막아서, 데이터를 외부에서 잘못 변경하는 것을 막고 변경 로직을 한 곳으로 제한하여 요구사항 변경에도 유연하게 대응할 수 있다. [[참고]](https://www.inflearn.com/questions/682636/%EC%BA%A1%EC%8A%90%ED%99%94-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C)
   - JavaScript 캡슐화 방법 [[참고]](https://ko.javascript.info/private-protected-properties-methods)
2. 절차적 프로그래밍
   - 프로그램을 수행해야 하는 일련의 단계로 나누고, 각 단계는 프로시저(procedure) 또는 함수로 표현되는 프로그래밍 패러다임
   - 객체지향 프로그래밍의 반대말이 아니다!
3. 휴리스틱(heuristic)
   - 컴퓨터학 용어 : 문제 해결이나 결정을 위해 사용되는 규칙 또는 경험적인 방법
   - 심리학 용어 : 복잡한 과제를 간단한 판단 작업으로 단순화시켜 의사 결정하는 경향
   - 경제학 용어 : 어떤 사안 또는 상황에 대해 엄밀한 분석에 의하기보다 제한된 정보만으로 즉흥적/직관적으로 판단/선택하는 의사결정 방식
