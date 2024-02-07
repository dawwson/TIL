# 클린 코드(Clean Code)

`#노개북` `#노마드코더` `#개발자북클럽`

- 작가 : 로버트 C. 마틴
- 출판사 : 인사이트

<br>

## Assignment #10

`2024.02.06`

### 🔖 7. 오류 처리

#### 기억하고 싶은 내용

[ 클린 코드와 오류 처리의 상관 관계 ]

- 뭔가 잘못될 가능성은 늘 존재한다.

- 오류 처리가 여기저기 흩어져 있으면 코드가 하는 일을 파악하기 어렵다.

<br>

[ 우아하게 오류를 처리하는 방법 ]

1. 오류 코드보다 예외를 사용하자

   - 오류 처리 코드와 뒤섞이지 않게 예외를 던지자.

2. Try-Catch-Finally 문부터 작성하자

   - try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다.(트랜잭션처럼)

   - 예외가 발생할 코드를 짤 때는 try-catch-finally 블록으로 시작하면 좋다.

   - 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성하면 좋다(TDD).

3. 미확인 예외를 사용하자

   - 확인된 예외는 OCP를 위반한다.

     - OCP : 새로운 기능이나 동작을 추가할 때에는 기존 코드를 변경하지 않고 확장할 수 있어야 한다.

   - 메서드 선언부에 throws를 사용하여 메서드가 반환할 예외를 모두 열거하는 방식을 지양하자.

   - 하위 레벨의 메서드에서 기능을 수정하여 새로운 예외를 던진다면 상위 레벨의 메서드를 호출하는 모든 곳에서 코드를 수정해야 한다.

     1. catch 블록에서 새로운 예외를 처리하거나 ✅
     2. 선언부에 throw 절을 추가한다. ❌

4. 예외에 의미를 제공하자

   - 오류 메시지에 정보를 담아 예외와 함께 던진다.
   - 실패한 `연산의 이름`과 `실패 유형`을 언급한다.
   - catch 블록에서 로깅 등을 통해 오류를 기록할 수 있도록 충분한 정보를 넘겨준다.

5. 외부 라이브러리가 발생시키는 예외를 감싸자

   - 외부 라이브러리를 감싸는 클래스와 거기서 발생시키는 예외를 감싸는 예외 클래스를 만들자
   - 외부 라이브러리와 프로그램 사이의 의존성을 줄일 수 있다.

     ```java
     LocalPort port = new LocalPort(12);
     try {
        port.open();
     } catch (PortDeviceFailure e) {
        reportError(e);
        logger.log(e.getMessage(), e);
     } finally {
        //...
     }

     public class LocalPort {
        private ACMEPort innerPort;

        public LocalPort(int portNumber) {
           innerPort = new ACMEPort(portNumber);
        }

        public void open() {
           try {
              innerPort.open();
           } catch (DeviceResponseException e) {
              throw new PortDeviceFailure(e);
           } catch (ATM1212UnlockedException e) {
              throw new PortDeviceFailure(e);
           } catch (GMXError e) {
              throw new PortDeviceFailure(e);
           }
        }
     }
     ```

6. 특수 사례 패턴(Special Case Pattern)

   - 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식이다. 클래스나 객체가 예외적인 상황을 캡슐화해서 처리하므로 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다. [[사례 참고]](https://doosicee.tistory.com/entry/%EA%B2%BD%EA%B3%84-%EC%99%B8%EB%B6%80-API%EB%A5%BC-%EB%8C%80%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EC%8B%A4%EC%A0%84%ED%8E%B8)

7. null을 반환 & 전달하지 않기
   - null 반환 또는 전달은 NullPointerException을 발생시킬 수 있는 위험을 내포한다.
   - 호출하는 함수 또는 호출되는 함수가 null 인지 확인하지 않은 채 사용하면 예외가 발생할 수 있다.
   - 매번 null인지 체크하는 로직 때문에 코드의 복잡성이 증가한다.

#### 오늘 읽은 소감

- 6장부터 슬슬 어려워진다...🤯
- 함수의 매개변수로 null을 전달하지 않도록 하고, null이 반환되지 않도록 하는 것이 좋다는 것을 보고 또 새로운 것을 알게 되었다. 그동안 메서드를 만들면 무언가를 전달해서 처리하기에만 급급했지, 어떻게 일관되게 null을 처리할 지에 대해 깊게 고민해보지 않았던 것 같다. 반성...ㅠ
- 특수 사례 패턴이 잘 이해가 안 되는데, 좀 더 사례를 찾아봐야겠다.

#### 궁금한 내용 & 잘 이해되지 않는 내용

1.  에러 vs 예외

    - 에러(Error)

      - 프로그램이 실행 중에 발생하는 심각한 문제
        - 메모리 부족, 파일을 찾을 수 없음, 네트워크 연결 문제 등
      - 대개 시스템 수준의 문제로, 프로그램이 중단된다.

    - 예외(Exception)
      - 프로그램이 실행 중에 `예측 가능`한 오류 상황에서 발생하는 문제
      - 잘못된 입력, 파일 읽기 오류, 배열의 인덱스 오류 등
      - 프로그래머가 예외 처리를 통해 프로그램에서 해결할 수 있다.

2.  확인된 예외 vs 미확인 예외

    - 확인된 예외(Checked Exception)

      - 컴파일러가 예외처리를 강제하는 예외
      - 파일 입출력 예외, SQL 예외 등
      - 예외처리가 되어야 컴파일 에러가 발생하지 않는다.

        ```java
           // 1. catch 블록으로 예외를 처리하는 방법
        public void readFile(String fileName) {
           try {
              // 파일을 열어서 읽는 작업 수행
           } catch (FileNotFoundException e) {
              // 파일을 찾을 수 없는 경우 처리
           } catch (IOException e) {
              // 입출력 예외 처리
           }

        }

        // 2. 예외 던지기(함수 선언부에 throws를 사용하여 호출자에게 예외를 처리할 의무를 부여함)
        public void readFile(String fileName) throws IOException {
            // 파일을 열어서 읽는 작업 수행
        }
        ```

    - 미확인 예외(Unchecked Exception)

      - 컴파일러가 예외 처리를 강제하지 않는 예외. 런타임 시에 발생할 수 있다.
      - NullPointerException, ArrayIndexOutOfBoundsException, ArithmeticException 등

    - 예외 처리 코드를 작성하지 않아도 되지만, 프로그래머가 적절한 예외 처리 해주지 않으면 프로그램이 예기치 않게 종료될 수 있다.

#### 나만의 공부법
<img width="671" alt="스크린샷 2024-02-06 오후 10 53 01" src="https://github.com/dawwson/TIL/assets/45624238/de13f6fb-8c04-4c7c-b13a-8b68f230daff">
