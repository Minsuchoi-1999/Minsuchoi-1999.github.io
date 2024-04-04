---
date: 2024-04-04 12:38:46
layout: post
title: "객체지향프로그래밍(25) - explicit 키워드와 mutable 키워드"
subtitle: "explicit, mutable 키워드 이해"
description: explicit과 mutable 키워드를 이해한다.
image: https://res.cloudinary.com/dtloik0ts/image/upload/v1710305020/62209a189cc9185b70db045b_6ea4ua9rZnloCziXgO4TM7Zqa5oWwYrMD4Lc5BqWGYHrJreJ0-Cq-VTOChRhm1IEhqCGeGpQh9M8L516rerUQF9l1FnfQaHEFyTzex7ily50AmFoRns3jMWLyd5edWCJqBbqGzvo_xaa8bg.jpg
optimized_image: https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1710305020/62209a189cc9185b70db045b_6ea4ua9rZnloCziXgO4TM7Zqa5oWwYrMD4Lc5BqWGYHrJreJ0-Cq-VTOChRhm1IEhqCGeGpQh9M8L516rerUQF9l1FnfQaHEFyTzex7ily50AmFoRns3jMWLyd5edWCJqBbqGzvo_xaa8bg.jpg
category: 객체지향프로그래밍
tags:
  - 정리
  - 객체지향프로그래밍
author: Minsu Choi
paginate: false
---

<h2>🔚 짧게 하는 복습</h2>

✅ 1. 기본 연산자와 클래스 간의 형변환 방법을 안다.

✅ 2. 다른 클래스 간의 형변환 방법을 모두 알고, 그 특징을 안다.

혹시 기억이 안 난다면, <u><a href = "/객체지향프로그래밍(24)-형변환의-모든-것/"> 다시 돌아가자</a></u>

---

지금까지 오버로딩 및 형변환에 다루면서, 지속적으로 제기되었던 문제가 있다.

바로 **묵시적 형변환**이다.

이는 우리가 원하지 않는 방향으로 코드가 진행될 수 있음을 뜻한다.

이 경우가 최악의 경우인데 **에러 하나없이 돌아가는데 의도치 않는 결과가 나와서 어디가 틀린지도 모르게 된다**.

오늘은 이를 막아주는 **explicit** 키워드와 또 다른 키워드 **mutable**에 대해서 다루겠다.

---

## explicit이란?

묵시적 형변환이 문제를 일으키는 코드를 하나 같이 보겠다.

<iframe height="750px" width="700px" src="https://www.interviewbit.com/embed/snippet/4c221c338d949d1c28bf"></iframe>

위 코드가 **컴파일(실행 전에) 에러**를 일으키는가? 아니다.

위 코드가 **런타임(실행 시간에) 에러**를 일으키는가? 그것도 아니다.

그런데 의도치 않은 상황이 발생했는가? **그렇다.**

분명 들어가면 안될 것이 들어갔는데, 코드가 실행이 되었다.

이런 것을 막기 위한 키워드가 **explicit**이다.

**explicit**이란 한국어로 변환하면 **명시적**이란 뜻으로, 말 그대로 의도치않은 묵시적 변환이 일어날 것 같으면 오류를 나타내는 키워드이다.

저기서 아래 코드처럼 1변수 생성자에 explicit을 넣으면, 함수 호출 과정에서 오류를 던져준다.

```c++
    explicit counter(int _count) : count(_count) {}
```

결과

```js
solution.cpp:22:13: error: could not convert '3' from 'int' to 'counter'

   22 |     display(3);

      |             ^

      |             |

      |             int
```

이 키워드가 왜 필요하냐면, **묵시적 변환으로 인한 오류를 컴파일 시간에 잡을 수 있기 때문이다.**

언급했던 것처럼 컴파일에도 런타임에도 잡히지 않는 오류를 몇 시간 동안 코드를 뒤지는 것은 정말 힘들기 때문이다.

유일한 단점이라 하면, **대입 연산자를 통한 일 변수 생성자를 쓰지 못한다**는 점 정도가 있다

**그러나, 사실 위의 이점에 비해서 이 정도 단점은 아무 것도 아니다.**

---

## mutable 키워드

**mutable 키워드**란 **const로 생성된 객체나 메소드에서 특정 멤버만 수정할 수 있는 권한**을 주는 키워드이다.

사실 본인도 공부하면서 지금까지 이 설명보다 이해가 잘 되는 것이 없어서, 이번 만큼만 출처를 남기고 원문을 가져와 보겠다. (출처 : <a href = "https://modoocode.com/253">_더 읽어보기_<a>)

_먼저 멤버 함수를 왜 const 로 선언하는지 부터 생각해봅시다. 클래스의 멤버 함수들은 이 객체는 이러이러한 일을 할 수 있습니다 라는 의미를 나타내고 있습니다._

_그리고 멤버 함수를 const 로 선언하는 의미는 이 함수는 객체의 내부 상태에 영향을 주지 않습니다 를 표현하는 방법 입니다. 대표적인 예로 읽기 작업을 수행하는 함수들을 들 수 있습니다._

_대부분의 경우 의미상 상수 작업을 하는 경우, 실제로도 상수 작업을 하게 됩니다. 하지만, 실제로 꼭 그렇지만은 않습니다. 예를 들어서 아래와 같은 서버 프로그램을 만든다고 해봅시다._

```c++
class Server {
  // .... (생략) ....

  // 이 함수는 데이터베이스에서 user_id 에 해당하는 유저 정보를 읽어서 반환한다.
  User GetUserInfo(const int user_id) const {
    // 1. 데이터베이스에 user_id 를 검색
    Data user_data = Database.find(user_id);

    // 2. 리턴된 정보로 User 객체 생성
    return User(user_data);
  }
};
```

_이 서버에는 GetUserInfo 라는 함수가 있는데 입력 받은 user_id 로 데이터베이스에서 해댱 유저를 조회해서 그 유저의 정보를 리턴하는 함수 입니다. 당연히도 데이터베이스를 업데이트 하지도 않고, 무언가 수정하는 작업도 당연히 없기 때문에 const 함수로 선언되어 있습니다._

_그런데 대개 데이터베이스에 요청한 후 받아오는 작업은 꽤나 오래 걸립니다. 그래서 보통 서버들의 경우 메모리에 캐쉬(cache)를 만들어서 자주 요청되는 데이터를 굳이 데이터베이스까지 가서 찾지 않아도 메모리에서 빠르게 조회할 수 있도록 합니다._

_물론 캐쉬는 데이터베이스만큼 크지 않기 때문에 일부 유저들 정보 밖에 포함하지 않습니다. 따라서 캐쉬에 해당 유저가 없다면 (이를 캐쉬 미스-cache miss 라고 합니다), 데이터베이스에 직접 요청해야겠지요. 대신 데이터베이스에서 유저 정보를 받으면 캐쉬에 저장해놓아서 다음에 요청할 때는 빠르게 받을 수 있게 됩니다._

_보통 한 번 요청된 정보는 계속해서 요청될 확률이 높기때문에 캐쉬에 넣게 됩니다. 물론 캐쉬 크기는 한정적이니까 이전에 오래된 캐쉬부터 지우게됩니다. 이를 구현한다면 아래와 같겠지요._

```c++
class Server {
  // .... (생략) ....

  Cache cache; // 캐쉬!

  // 이 함수는 데이터베이스에서 user_id 에 해당하는 유저 정보를 읽어서 반환한다.
  User GetUserInfo(const int user_id) const {
    // 1. 캐쉬에서 user_id 를 검색
    Data user_data = cache.find(user_id);

    // 2. 하지만 캐쉬에 데이터가 없다면 데이터베이스에 요청
    if (!user_data) {
      user_data = Database.find(user_id);

      // 그 후 캐쉬에 user_data 등록
      cache.update(user_id, user_data); // <-- 불가능
    }

    // 3. 리턴된 정보로 User 객체 생성
    return User(user_data);
  }
};
```

_그런데 문제는 GetUserInfo 가 const 함수라는 점입니다. 따라서_

```c++
cache.update(user_id, user_data); // <-- 불가능

```

_위 처럼 캐쉬를 업데이트 하는 작업을 수행할 수 없습니다. 왜냐하면 캐쉬를 업데이트 한다는 것은 케쉬 내부의 정보를 바꿔야 된다는 뜻이기 때문이죠. 따라서 저 update 함수는 const 함수가 아닐 것입니다._

_그렇다고 해서 GetUserInfo 에서 const 를 떼기도 좀 뭐한것이, 이 함수를 사용하는 사용자의 입장에선 당연히 const 로 정의되어야 하는 함수 이기 때문이지요. 따라서 이 경우, Cache 를 mutable 로 선언해버리면 됩니다._

```c++
mutable Cache cache;  // 캐쉬!
```

_위 처럼 말이지요. 이렇듯, mutable 키워드는 const 함수 안에서 해당 멤버 변수에 const 가 아닌 작업을 할 수 있게 만들어줍니다._

---

✅ 1. explicit 키워드의 정의와 활용을 안다.

✅ 2. mutable 키워드의 정의와 활용을 안다.

⚠️ 복잡한 프로그램이나 오버 로딩을 많이 하게되면 explicit 생성자를 최대한 활용하자.

💣 과제, 없음

🔜 더 공부해보기,

<a href = "https://modoocode.com/253">읽어볼 거리(1) - 모두의 코드 전문</a>
