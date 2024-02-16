# Boyer-Moore 과반수 투표 알고리즘

- Boyer-Moore 과반수 투표 알고리즘(Boyer-Moore majority vote algorithm)은 주어진 후보자 목록에서 과반수를 차지하는 후보를 찾는 알고리즘이다.

- 배열 내 과반수에 해당하는 원소가 항상 존재한다고 가정하면, 결과값은 항상 과반수 원소가 된다.

- 핵심 아이디어는 주어진 후보자 목록에서 두 가지의 서로 다른 후보를 지정하고, 이들을 서로 상쇄시켜 가며 최종 후보를 결정하는 것이다.

- 시간 복잡도는 O(N), 공간 복잡도는 O(1)이다.

## 동작 방식

1. 입력 시퀀스 요소 m과 카운터 c를 0으로 초기화한다.

2. 입력 시퀀스에서 각각의 요소 x에 대해서

   1. c == 0이면 m = x, c = 1 대입한다.
   2. m == x이면 c = c + 1 대입한다.
   3. m != x이면 c = c - 1 대입한다.

3. m을 반환한다.

<br>

이를 비유를 들어서 내가 이해한 대로 설명해보자면...

- 한 교실에 학생들이 모여있다.
- 점심 메뉴를 햄버거와 피자 중에서 고를 건데, 학생들이 더 많이 선택한 메뉴로 선정할 것이다.
- 두 명씩 짝을 짓는다.
- 두 사람이 선택한 메뉴가 같다면 교실 안에 남는다.
- 두 사람이 선택한 메뉴가 다르다면 손 잡고 교실을 나간다.
- 교실에 남아있는 학생이 고른 메뉴로 최종 선택한다.

## 소스 코드 (JS)

```js
var majorityElement = function (arr) {
  let candidate = 0;
  let count = 0;

  for (let i = 0; i < arr.length; i++) {
    if (count == 0) {
      candidate = arr[i];
      count = 1;
    } else if (candidate == arr[i]) {
      count++;
    } else {
      count--;
    }
  }
  return candidate;
};
```

## 참고

> 1. 블로그 - [Boyer-Moore 과반수 투표 알고리즘](https://sgc109.github.io/2020/11/30/boyer-moore-majority-vote-algorithm/)
> 2. 위키백과 - [Boyer–Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)
> 3. 관련 알고리즘 문제 - LeetCode 169번 Majority Element
>    - [문제 풀이](https://github.com/dawwson/algorithm/blob/main/leetcode/easy/169.%20Majority%20Element.md)
