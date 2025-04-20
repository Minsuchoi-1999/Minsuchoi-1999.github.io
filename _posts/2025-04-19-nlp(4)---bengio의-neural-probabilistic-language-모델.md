---
date: 2025-04-19 07:33:58
layout: post
title: "NLP(4) - N-gram의 한계와 Bengio의 Neural Probabilistic Language 모델이란?"
subtitle: A Neural Probabilistic Language Model 논문 리뷰
description: 신경 확률적 언어 모델에 대해 알아본다.
image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
optimized_image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_transformation/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
category: NLP, AI
tags:
    - NLP
    - AI 
author: Minsu Choi
paginate: false
---
지금까지는 Claude Shannon의 A Mathematical Theory of Communication을 리뷰해보았다.

주된 내용은 마르코프 프로세스를 기반으로 한 통계적 언어 모델인 N-gram, 각 정보원의 정보량 계산 방식, 그리고 이를 직접 구현해보는 시간으로 구성되었다.

오늘은 N-gram 한계, 그리고 이 한계를 근본적으로 해결하고자 시도한 Yoshua Bengio의 신경 확률적 언어 모델(A Neural Probabilistic Language Model)의 도입 배경에 대해 알아보겠다.


---

### 1. 차원의 저주(curse of dimensionality)

결국 N-gram 모델의 핵심 문제는 모든 가능한 N-gram 조합의 확률을 저장해야 한다는 점이다.

예를 들어, N이 2이고 알파벳 a~z와 공백(‘ ’)까지 총 29개의 기호를 사용한다면, 가능한 조합은 aa, ab, ..., ' '처럼 총 29 × 29 = 841개가 된다.

N이 3이라면 29³ = 24,389개의 조합이 필요하다. 즉, 기호의 개수를 M이라 할 때 총 조합 수는 Mⁿ으로 일반화할 수 있고,

N이 커질수록 모든 조합을 저장하기 위한 메모리와 계산량이 기하급수적으로 증가한다.

이처럼 차원이 증가함에 따라 생기는 계산 복잡도의 폭발을 동적 프로그래밍에서는 **차원의 저주(curse of dimensionality)**라고 부른다.

그런데 현실에서는, N-gram을 글자 단위가 아니라, 유의미한 문장을 만들기 위해 단어 단위로 사용하는 경우가 많다.

이렇게 되면 단어 수만 이미 수천 ~ 수만 개에 달하기 때문에, 조합 수는 훨씬 더 폭발적으로 증가하고, 이에 따라 메모리와 계산량 문제는 더욱 심각해진다.

---

### 2. N-gram sparsity

두 번째 문제는, 지금까지의 N-gram 모델은 학습데이터에 과의존한다는 것이다. 이 문제는 일반적으로 **N-gram 희소성 문제(N-gram sparsity)**라고 불린다.

즉, 학습되지 않은 연속적인 단어 N개가 나타났을 때 N-gram은 정확도를 크게 잃는다.

물론 이를 더 작은 단어 단위로 줄여서 N-1개를 통해 예측하거나(Back-off 기법), N, N-1, ... 1개의 확률을 모두 사용하는 보간법(Smoothing/interpolation) 기법도 존재하나 이는 오히려 모든 차원의 수를 저장해야한다는 점에서 차원의 저주에 다시 갇히게되는 문제가 발생한다.

---

### 3. 핵심 아이디어

논문에 적힌 표현을 그대로 옮기면, 다음과 같다.

1. 각 단어에 대해 분산 표현(distributed feature vector)을 할당한다.
→ 실수 벡터 공간에서의 위치로, 단어 간 유사성을 정의할 수 있다.

2. 단어 시퀀스의 결합 확률(joint probability)을 이 벡터들로 표현한다.
→ 즉, 단어 자체가 아니라 단어의 벡터 표현을 입력으로 확률을 계산한다.

3. 단어의 feature vector들과 확률 함수를 동시에 학습한다.
→ 단어 임베딩과 모델 파라미터를 함께 학습하는 방식이다.

단어들이 좀 어렵긴 한데, 논문에서는 이렇게 예시를 들어 설명한다.

앞에서 말했듯이, 우리가 "dog"랑 "cat"이 비슷한 단어라는 걸 알고 있다면, "The cat is walking in the bedroom"이라는 문장을 학습한 모델은 "A dog was running in a room" 같은 문장도 자연스럽게 잘 예측할 수 있어야 한다.

즉, 비슷한 단어는 비슷한 벡터를 갖게 만들고, 확률 함수도 그 벡터에 부드럽게 반응하도록 설계돼 있어서 벡터가 살짝 달라진다고 확률이 크게 흔들리지 않는다. *(확률 함수는 연속 함수기 때문이다)*

결국 문장 하나만 봐도, 그 문장이랑 비슷한 수많은 조합들까지 같이 높은 확률을 줄 수 있는 구조를 도입했다는 뜻이다.

**단순한 N-gram식 조합 통계가 아니라, 확률 공간 안에서 비슷한 문장을 자연스럽게 일반화해서 다룰 수 있다는 뜻이다.**

다음 포스트부터는, 이를 이해하기 위해 차근차근 순서대로 공부해보겠다.
