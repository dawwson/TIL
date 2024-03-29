# 클린 코드(Clean Code)

`#노개북` `#노마드코더` `#개발자북클럽`

- 작가 : 로버트 C. 마틴
- 출판사 : 인사이트

<br>

## Assignment #15

`2024.02.15`

### 독후감

사실 '클린 코드'를 읽기 전에 이 책에 대한 선입견이 있었다. 개발자 커뮤니티나 개발 유튜버들이 '클린 코드는 해롭다', '클린 코드를 추천하지 마라', '클린 코드의 광신도들이 문제다'라는 말까지 봐왔던 탓일까...😂 그럼에도 불구하고 추천하는 개발자들도 많이 있기에 어떤 내용일지 궁금해졌다. 비판을 하더라도 읽어보고 판단하는 것이 좋지 않을까? 사람마다 느끼는 것은 다를 것이고, 무엇보다도 새롭게 배우는 것이 더 많을 것이란 기대감으로 읽어 나갔다.

1장부터 10장까지의 내용 중 기억나는 부분, 실제로 적용해보고 싶은 부분을 정리해보자면 다음과 같다.

- 깨끗한 코드는 읽으면서 짐작한 대로 돌아가는 코드다.

- 이름을 통해 적절한 정보를 제공하여 의미와 의도를 분명히 하자.

- 따로 주석이 필요하다면 의도가 분명하게 드러나지 않은 것이다.

- 컨테이너 유형을 이름에 넣지 않는 것이 좋다.

- 검색 가능한 이름을 사용하자.

- 함수는 한 가지 일만 해야한다.

- 함수는 상태를 변경하거나 정보를 반환하거나 둘 중 하나만 해야 한다.

- 함수의 이상적인 매개변수는 2개 이하이다. 그보다 많다면 클래스로 개념을 묶는다.

- 코드 내용을 그대로 설명하는 주석은 나쁜 주석이다.

- 주석을 다는 것은 필수적인 것이 아니다. 좋은 코드는 주석보다 코드 자체로 말한다.

- 구현 의도 설명, 주의할 내용, 중요성을 강조하는 부분, TODO 정도면 주석은 충분하다.

- 서로 밀접한 코드는 가까이 놓는다.(개념적 유사성)

- 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치시킨다.호출하는 함수 바로 아래에 호출되는 순서대로 배치하면 더 좋다.(고차원 -> 저차원)

- 예외에 의미를 제공하자. 오류 메시지에 정보(연산의 이름, 실패 유형)를 담아 catch 블록에서 로깅할 수 있도록 예외와 함께 던진다.

<br>

클린 코드에서 주창하는 것을 융통성 없이 적용했을 때의 문제점을 생각해보았다. [[참고1]](https://medium.com/@aronisfotis/why-clean-code-practices-can-be-harmful-a2ba141ca5df) [[참고2]](https://velog.io/@leejh3224/%EB%B2%88%EC%97%AD-%EC%9E%98%EB%AA%BB%EB%90%9C-%EC%B6%94%EC%83%81%ED%99%94)

- 오버엔지니어링으로 이어질 수 있다. 가독성을 위해 모두 추상화하고 분리하다 보면, 오히려 이해도 어렵고 기능을 추가하거나 수정하기 어려운 코드를 만들어낼 수도 있다.(과도한 추상화)

- 깨끗하고 완벽한 코드를 작성하는 것에만 집중하여 제품의 데드라인을 놓칠 수 있다.

<br>

어떤 원칙이든 그것을 쓰기 적절한 상황이 있고 그렇지 않은 상황이 있다. 현재 해결해야 하는 문제는 무엇인지를 생각하면서 적용해야 의미가 있는 것이지, 무조건 어떠한 원칙을 따라야 한다!는 생각은 위험한 것 같다. 책의 서문과 초반 내용에서도 클린 코드의 원칙을 절대적으로 따르기 보다는 오랜 시간 개발자들이 고민한 내용을 이해하는 것에 중점을 두라고 이야기한다. 아마 원칙을 지키는 것에 몰두하여 협업을 힘들게 하는 개발자를 겪은 사람들이 클린 코드에 대한 날선 비판을 한 것이 아닐까...? 중요한 것은 책을 통해 얻은 지식을 나에게 익숙한 언어로, 나만의 인사이트로 정리하여 코드에 적용하는 것이라고 생각한다.

나는 개인적으로 이 책을 읽으면서 동의하기 어려운 부분도 일부 있었으나 반성도 많이 하게 되었다. 실제 나의 코드에 적용하고 싶은 부분도 많았고, 협업에 필요한 인사이트도 많이 얻었다. '아, 이게 맞는 거 같은데?', '이건 아닌 거 같은데?' 이렇게 직감적으로 느끼고 코딩해왔던 것들에 대한 이유를 정리할 수 있었다.

Best Practice는 있지만 정답은 없는 것 같다. 그럼에도 확실히 정답이라고 느끼는 문장이 있었다. 하나는 코드가 위에서 아래로 이야기처럼 읽혀야 한다는 것이고, 다른 하나는 의미와 의도를 분명히 해야한다는 것이었다. 이 두 가지는 계속 기억하면서 코드를 작성하고 싶다.
