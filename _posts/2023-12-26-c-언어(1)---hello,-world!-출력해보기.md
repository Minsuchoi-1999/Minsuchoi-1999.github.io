---
date: 2023-12-26 05:37:39
layout: post
title: "C 언어(1) - Hello, World! 출력해보기"
subtitle: "C 언어 강의 - 오리엔테이션"
description: 가장 간단한 코드를 직접 짜고 확인해보는 시간을 가진다.
image: https://res.cloudinary.com/dtloik0ts/image/upload/t_transformation/v1703569833/programming-careers-s_lvchit.jpg
optimized_image: https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1703569833/programming-careers-s_lvchit.jpg
category: C
tags:
  - C
  - 정리
author: Minsu Choi
paginate: false
---

C언어를 복습하고 정피할 겸 포스팅하는 것으로 결정했다.

C언어를 많은 학생에게 가르치면서도 다시 느꼈던 것이지만 절대 쉬운 언어가 아니다. (사실 코딩이라는 것이 쉬운 것이 아니다.)

하지만 새로운 블로그도 만든 김에, 천천히 따라올 수 있도록 해보겠다.

오늘 할 내용은 오리엔테이션의 국룰이라고 할 수 있는 Hello World를 출력해보는 것을 하겠다. (컴공계의 전통과도 같다.)

Visual Studio를 설치하거나, 온라인 컴파일러를 이용해서 IDE를 켜주고 아래의 코드를 그대로 입력한다.

그리고 실행시키고 아래의 코드 실행결과와 비교해본다.

<iframe height="375px" width="700px" src="https://www.interviewbit.com/embed/snippet/3c04301f344df5c1d004"></iframe>

축하한다. 여러분은 무슨 뜻인지는 아직 모르지만, 첫 프로그램을 만드는 데에 성공한 것이다.

우선은 아래의 양식을 외우도록 하자. 아직은 이해하기보다는 이 양식을 외워서 쓰는 편이 편하기 때문이다.

```js
#include<stdio.h>

int main(){
    //이 부분

    return 0;
}
```

그리고 "이 부분"이라고 표시되어있는 부분에 코드를 넣으면 프로그램을 만들 수 있다.

코드는 당연히 여러 가지 종류가 있지만, 오늘 수업은 printf만 배울 것이다.

<h2>printf란? </h2>

printf란 print + format을 합쳐서, 말 그대로 format을 갖춰서 출력하는 기능을 한다.

쉽게 말해서, 출력하는데 쓰는 함수라는 것이다. 한동안은 출력은 printf만 다룰 것이기 때문에 편하게 출력하면 printf를 떠올리면 좋다.

printf()의 괄호 안에 어떤 글자든 " "안에 입력하면, 그대로 출력이 된다. (다른 글자로도 실험을 해보자)

그런데 여기서 이런 질문이 들면 훌륭하다.

**"그럼 내가 직접 입력한 문자만 고정된 채로 출력할 수 있는 건가?"**

당연히 그렇지 않다. 다음 수업은 fix 되지 않은, 즉 값에 따라 변할 수 있는 문장을 출력하는 것을 배우도록 하겠다.

<h2>📖 오늘의 핵심(이걸 다 알기 전까지는 넘어가지 말자❗)</h2>

✅ 1. 코드 양식을 외우자

✅ 2. printf()는 ()안의 문자를 그대로 출력하는 기능을 한다..

⚠️ 오타에 주의하자. studio가 아니다, stdio다

💣 과제, 인사를 했으면 자기소개를 하는 것이 국룰이다.
My name is Minsu를 출력해보자(난이도 下)
