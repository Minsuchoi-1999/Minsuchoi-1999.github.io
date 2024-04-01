---
date: 2024-03-31 10:46:53
layout: post
title: "객체지향프로그래밍(9) - const와 static, 이름 공간"
subtitle: "const와 static 이해, 이름 공간의 이해"
description: 클래스에 const와 static을 이용해 특정 기능을 추가해보고 이름 공간을 이해한다.
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

✅ 1. 참조자의 정의를 알고, 포인터와의 차이를 안다.

✅ 2. 참조자의 특징과 장점을 안다.

✅ 3. 복사 생성자가 무엇인지 알고, 디폴트 복사 생성자를 오버로딩해서 복사 생성자를 만들 수 있다.

혹시 기억이 안 난다면, <u><a href = "/객체지향프로그래밍(8)-참조자와-복사-생성자/"> 다시 돌아가자</a></u>

---

저번 강의까지 생성자 오버로딩, 복사 생성자, 소멸자까지 일차적인 클래스의 기본 개념은 모두 배웠다.

앞으로 배울 내용은 여기서 클래스 간의 관계, 객체들을 쉽게 다루는 방법 등이다.

진짜 클래스 자체를 다루는 방법은 오늘이 마지막이다.

오늘은 **const**와 **static**라는 기능으로 클래스에 추가 기능 제어를 하는 방법에 대해 알아보겠다.

사실 c에서 배운 내용과 크게 다르지 않으므로, 걱정할 필요없다.

---

## const란?

**const**란 변수 앞에 붙여서 그 **변수를 상수로 취급**하는 것이다.

즉, 어떠한 변수를 바뀌지 못하게 고정하는 것을 의미한다.

C에서 기본적인 자료형의 변수 혹은 배열이나 구조체에도 const를 붙였던 것처럼, C++의 객체에도 const를 붙일 수 있다.

또한, class 내의 변수에도 const를 붙여서 수정을 불가하게 할 수도 있다.

```c++
#include<iostream>

class human{
    private:
    const std::string name; // 어떤 객체든, 한 번 정해진 이름은 변경 불가
    int age;

    public:
    human() : name("unknown"), age(1) {} // 디폴트 생성자

    human(std::string _name) : name(_name), age(1) {} // name 변수 생성자

    human(const human& target) : name(target.name) {std::cout<<"Copy Constructor!!"<<std::endl;} // 복사 생성자
    //원본이 바뀌면 안 되니 const

    void get_name(){
        std::cout<< "My name is "<<name<<std::endl;
    }

    /*
    void set_name(std::string name){
        name = _name;
    }
    */
    //name이 const라서 변경 불가

    void set_age(int _age){
        age = _age;
    }
};

int main(){
    const human Minsu = human("Minsu"); // const이기에 Minsu의 모든 속성 변경 불가

    Minsu.set_age(17); // 불가
}

```

여기서 C와 다르게 흥미로운 점은, C++에서는 함수에도 const를 붙일 수 있다.

이때는 const를 뒤에 붙여주는데, 이 함수 내에서는 객체의 어떤 속성도 변경하지 않는다는 뜻을 가진다..

예를 들어 get_name은 객체의 이름만 가져오면 된다. 수정할 사항이 전혀 없다.

그렇기에 const 함수로 지정할 수 있다.

```c++
    void get_name() const{
        std::cout<< "My name is "<<name<<std::endl;
    }
```

const를 설정하는 이유는, 프로그래머의 실수로 원치 않는 수정이 일어날 수 있으니 미리 const로 예방하기 위함이다.

---

## static이란?

static이란 어떠한 변수 선언 앞에 붙여서, 전역 변수처럼 다루는 역할을 했다.

이렇게 static을 붙여주면 data 영역에서 생성되기에 프로그램 종료 전까지는 그 값을 유지하는 것이 특징이다.

보통 한 번만 초기화를 하고, 그 뒤에 변수 선언이 다시 되어도 모두 무시된다.

우선 아래의 예제를 먼저 보자.

<iframe height="750px" width="700px" src="https://www.interviewbit.com/embed/snippet/7c262cda051d11fc6341"></iframe>

분명 함수 안에서 선언했지만, static int는 data 영역에서 보존되고, int는 스택 프레임이 생기고 없어질 때마다 동시에 생기고 사라진다.

이런 것처럼 클래스 내에 static 변수를 선언하게 되면, 객체와 무관하게 static 변수는 data 영역에서 보존된다.

아래의 응용 예제를 보자.

<iframe height="1500px" width="700px" src="https://www.interviewbit.com/embed/snippet/6dc6474fcb79b100e6ec"></iframe>

inc*test::이 무엇인지는 조금만 있다가 설명하겠다. *(쉽게 말하면 inc*test라는 클래스 안에 있다는 뜻이다.)*

또한, const처럼, class 내에서 static 함수도 선언할 수 있다.

이는 static 변수처럼 전역 범위에서 다루기에, 객체를 생성하지 않아도 사용 가능한 함수를 말한다.

직접 보며 이해하자.

<iframe height="375px" width="700px" src="https://www.interviewbit.com/embed/snippet/6e703da664d2eedf6fb2"></iframe>

---

## 범위 지정자 ::

그나저나, **::**라는 기호가 굉장히 익숙하다. std::string, std::endl, std::cout 등 많은 곳에서 봐왔다.

이게 무슨 의미인지 알아보자. 결과부터 말하자면 **::**는 **변수나 함수가 어디서 왔는지를 알려주는 범위연산자**이다.

C 언어에서 같은 이름의 함수나 변수를 사용하는 것은 불가능하다. 이는 분명히 C++도 같다.

하지만 어떤 클래스의 어떤 변수, 어떤 공간에서의 어떤 함수 등 범위가 지정된다면 이야기가 달라진다.

보통 ::의 왼쪽에는 **클래스**나 **이름 공간(namespace)**라는 것이 오고, 오른쪽에는 그 공간 내에서의 변수나 함수 이름을 적어서 구체화한다..

아래와 같이 사용할 수 있다.

```c++
#include <iostream>

class human{
    public:
    static int age;
};

class cat{
    public:
    static int age;
};

class dog{
    public:
    static int age;
};

int human::age = 5;
int cat::age = 10;
int dog::age = 15;

int main() {
    std::cout<< human::age<<" "<<cat::age<<" "<<dog::age<<"\n";
}

```

즉, **클래스에서 범위 지정자**는 **static으로 선언된 변수나 함수를 접근**할 때 사용된다.

눈치가 빠르다면 아래와 같은 추론을 할 수 있다.

'아하, ::앞에 붙는 클래스에서 온 변수나 함수라는 뜻이구나!

그러면 std::cin은 std라는 클래스에서 온 cin 변수이고, std::cout은 std라는 클래스에서 온 cout이라는 변수구나'

하지만 아쉽게도 약간 틀렸다.

C++에서는 라이브러리보다 한 단계 작은 규모인 **이름 공간(namespace)**라는 것이 존재한다.

std라는 것은 iostream이라는 라이브러리에 속한 하나의 **이름 공간**인 것이다.

---

## 이름 공간이란?

**이름 공간**이란 **변수, 함수, 클래스 등의 이름이 서로 충돌하지 않도록 만든 집합 관계**이다.

**이름 공간은 다른 이름 공간을 포함할 수 있고, 라이브러리는 여러 이름 공간을 포함할 수 있다.**

이것이 나오게 된 계기는 C++로 발전하면서 여러 라이브러리가 생기게 되었다.

또 많은 라이브러리를 사용하다 보니, 다른 라이브러리의 같은 이름의 함수나 변수가 문제를 발생시켰다.

예를 들어, 어떤 라이브러리에서는 max가 두 원소 중 최댓값을 반환하고, 다른 라이브러리에서 max라는 함수는 배열 전체에서 최대 원소를 반환한다고 하자.

어떤 라이브러리를 만들 때, 기존의 모든 라이브러리를 확인하며 중복되지 않는 변수명, 함수명을 가지는 것은 불가능하다.

그렇기에 이름 공간이라는 집합을 만들어서, 범위 지정자를 통해 어떤 이름 공간의 변수, 함수, 혹은 클래스를 말하는 것인지 구체화 시키는 방법을 선택했다.

이름 공간의 선언은 아래와 같다.

```c++
namespace (이름 공간명){
    //변수 선언
    //함수 선언
    //클래스 선언
}
```

더 구체적인 예는 아래의 코드를 보며 이해해보자.

<iframe height="375px" width="700px" src="https://www.interviewbit.com/embed/snippet/7d89a4ac90e120781080"></iframe>

같은 max이지만, 다른 이름 공간에서 선언되었고 범위 지정자를 잘 설정해준 덕분에 문제없이 실행된다.

그런데 이게 가끔은 귀찮을 때도 있다.

예를 들어, std::endl, std::cin, std::cout 등은 아무리 생각해도 우리가 다른 함수와 오인할 것 같지 않다.

이런 경우에는 int main() 위에

```c++
using namespace (이름 공간 이름)
```

을 선언하면서 생략할 수 있다.

즉, 이때까지 우리가 작성한 모든 **std::**는 아래의 코드처럼 **using namespace std;**를 통해서 생략할 수 있다.

```c++
#include<iostream>

using namespace std;

int main(){
    cout<<"hello world"<<endl;
}
```

다만, 이 경우는 매우 조심해야 하는 것이 중복되는 변수나 함수가 없다는 것이 확실해야 한다.

모든 라이브러리의 변수, 함수, 클래스가 **하나의 이름 공간으로 고정되기 때문이다.**

**원치 않는 변수나 함수, 클래스가 들어가게 되어 잡기 힘든 버그를 만들 수 있다.**

---

✅ 1. const가 무엇인지 알고, 어떻게 사용하는지 안다.

✅ 2. static이 무엇인지 알고, 어떻게 사용하는지 안다.

✅ 3. 범위 지정자 ::이 무엇인지 알고, 어떻게 사용하는지 안다.

✅ 4. 이름 공간과 using namespace를 이해한다.

⚠️ static 변수는 클래스 정의 밖에서 선언한다.

⚠️ 이름 공간을 using namespace를 사용할 때는, 이름 충돌을 주의해야 한다.

💣 과제,

1. (<a href = "https://minsuchoi-1999.github.io/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D(2)-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D(oop)%EB%9E%80/">포켓몬 코드</a>)를 지금까지 배운 객체지향 프로그래밍을 이용하여 작성해보자(난이도 上)

```

```
