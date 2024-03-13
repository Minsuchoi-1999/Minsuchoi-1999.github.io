---
date: 2024-03-13 04:25:18
layout: post
title: "객체지향프로그래밍(1) - Welcome to C++"
subtitle: "객체지향프로그래밍을 위한 간단한 C++ 요약"
description: C++ 이용
image: https://res.cloudinary.com/dtloik0ts/image/upload/v1710305020/62209a189cc9185b70db045b_6ea4ua9rZnloCziXgO4TM7Zqa5oWwYrMD4Lc5BqWGYHrJreJ0-Cq-VTOChRhm1IEhqCGeGpQh9M8L516rerUQF9l1FnfQaHEFyTzex7ily50AmFoRns3jMWLyd5edWCJqBbqGzvo_xaa8bg.jpg
optimized_image: https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1710305020/62209a189cc9185b70db045b_6ea4ua9rZnloCziXgO4TM7Zqa5oWwYrMD4Lc5BqWGYHrJreJ0-Cq-VTOChRhm1IEhqCGeGpQh9M8L516rerUQF9l1FnfQaHEFyTzex7ily50AmFoRns3jMWLyd5edWCJqBbqGzvo_xaa8bg.jpg
category: 객체지향프로그래밍
tags:
  - 정리
  - 객체지향프로그래밍
author: Minsu Choi
paginate: false
---

오늘부터 프로그래밍의 새로운 패러다임인 객체지향 프로그래밍(Object Oriented Programming)에 대해 배워보겠다.

그 전에 간단한 C++에 기본적인 문법을 다루어보겠다.

⚠️ 이 강의는 C언어의 기본 문법에 대해 안다는 전제 하에 진행된다. 만약 C언어의 기본 문법을 잘 모른다면 <a href = "c-언어(27)-강의를-마무리-하며/">C언어 강의 시리즈</a>와 <a href = "/자료구조(3)-동적-할당과-원리/">C언어 동적 할당</a>을 미리 공부하고 오는 것을 추천한다.

그럼 C 언어와 C++ 언어의 주요 문법을 비교하며 공부해보겠다.

---

## 1. 전체적인 틀

```c
// C언어
#include<stdio.h>
#define MAX 100

int arr[MAX];

void foo(){
    arr[0] = 1;
}

int main(){
    // 원하는 코드
}
```

```c++
// C++
#include<iostream>
#define MAX 100

int arr[MAX];

void foo(){
    arr[0] = 1;
}

int main(){
    // 원하는 코드
}
```

전체적인 틀은 거의 비슷하다.

**C언어에서 가능한 전처리, 전역변수 설정, 함수 선언 등은 모두 C++에서 가능하다.**

C에서는 표준 라이브러리든 사용자가 만든 라이브러리든 .h 확장자를 통해 include했으나, **C++에서는 표준 라이브러리는 .h를 붙이지 않아도 include 가능하다**.

위의 코드처럼 iostream.h에서 h를 생략한 것처럼 말이다.

또한, C언어에서 사용가능한 표준 라이브러리는 **모두 C++에서도 include 가능하다**. **(math.h -> cmath, string.h -> cstring으로 c를 붙여서 include 가능)**

---

## 2. 자료형과 입출력

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/9b9c46a1c2b8cf1f85b8"></iframe>

우리가 다룬 기본 자료형으로는 **정수형, 실수형, 문자형**이 있었으며 조금 확장해서 **문자열**까지 다루었었다.

stdio.h에 포함된 **scanf**와 **printf**를 보통 입출력에 사용했었고, 이름에 맞게 각각은 %d, %lf, %c, %s 등으로 formatting해줘야 했다.

주의할 점으로는 각각의 format을 맞추지 않으면 원하지 않는 값이 입력되거나 출력될 수 있었다.

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/a81dbd4075a4b200bd4e"></iframe>

C++의 경우에는 **iostream**에 포함된 **std::cin**과 **std::cout**을 입출력으로 사용한다. _(물론 C언어에서 사용되는 scanf, printf도 여전히 사용 가능하다.)_

그리고 개행문자 **'\n'** 대신 **std::endl**을 써서 줄 바꿈을 할 수도 있다.

여기서 혁신적인 기술이 나오는데, formatting 없이 **<<**와 **>>**로 입력과 출력이 가능하다. _(여기서 <<, >>는 비트연산자와 무관하다.)_

또한, 문자열을 전문으로 다루기 위한 **std::string**이라는 자료형이 추가가 된다.

정확히는 자료형이 아니라 **객체**라는 개념이지만, 큰 틀에서 자료형이라고 생각하고 넘어가도 무방하다.

std::string은 C언어의 문자열과는 다르게 **문자열끼리, 문자형 변수와 덧셈 연산**도 가능한데, 아래와 같은 코드가 가능하다.

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/372c25a33aa3414cbdd2"></iframe>

---

## 3. 조건문, 반복문

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/bed5339ee00c393b7630"></iframe>

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/d7bc3494e293dd28aa9e"></iframe>

C에서 조건문으로 **if, else if, else 혹은 switch**를 썼었는데, 위와 같이 모두 C++에서도 가능하다.

<iframe height= "700px" width= "700px" src="https://www.interviewbit.com/embed/snippet/7eecd27bd02a30248844"></iframe>

<iframe height= "700px" width= "700px" src="https://www.interviewbit.com/embed/snippet/dbab6e3e690d9331a5ac"></iframe>

반복문도 **for, while**을 통한 방식 모두 C++에서 사용이 가능하다.

## 4. 동적 할당

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/3311888d4b67ec914d1e"></iframe>

C언어에서는 **malloc, realloc** 등을 이용해서 할당, 재할당을 하고 **free**를 이용해서 할당해제를 했었다.

<iframe height="700px" width="700px" src="https://www.interviewbit.com/embed/snippet/b568b6803ffb8c9ef4c3"></iframe>

C++에서는 **new**와 **delete**라는 함수를 이용해서 할당, 할당해제를 한다.

**C++에서는 재할당 대신 new로 새로 만드는 것을 권장**하기 때문에, 번거롭더라도 새로운 메모리를 new로 할당하고 기존의 값을 복사한 후 delete로 해제해주면 된다.

```c++
new 자료형
// 자료형에 대한 크기만큼 동적할당해서 그 포인터를 반환(당연히 구조체, 공용체도 가능)
// 배열의 경우 자료형[]로 해주면 됨

delete 포인터변수
// 그 포인터에 할당된 메모리를 해제
// 배열의 경우 delete[] 포인터 변수를 해주면 됨
```

---

<h2>📖 오늘의 핵심(다 알기 전까지는 넘어가지 말자❗)</h2>

✅ 1. C++의 기본 문법에 익숙해지자.

✅ 2. iostream, std::cin, std::cout에 익숙해지자.

✅ 3. 이제는 malloc, realloc, free 대신 new, delete에 익숙해지자.

⚠️ C++에서는 웬만하면 문자열을 std::string을 쓰도록 하자, 버퍼 오버플로우도 막을 수 있고 훨씬 안전하다.

💣 과제,

1. +, -, /, \*와 숫자 2개를 입력받아 값을 계산하는 계산기를 C++ 구현해보자(난이도 下)

2. C++ BST를 구현해보자(난이도 中) (<a href = "/자료구조(11)-완전-이진-트리의-이진-탐색-트리의-구현/">BST가 무엇인지 모르겠다면?</a>)

🔜 더 공부해보기,

<a href = "https://velog.io/@msung99/%EC%8B%9C%ED%94%84-3-1%EC%A3%BC%EC%B0%A8">읽어볼 거리(1) - malloc/realloc/free 대신 new와 delete를 고려하게된 이유(C 포인터의 취약점)</a>
