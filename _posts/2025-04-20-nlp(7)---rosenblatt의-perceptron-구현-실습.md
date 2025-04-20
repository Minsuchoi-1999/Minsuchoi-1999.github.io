---
date: 2025-04-20 11:38:33
layout: post
title: "NLP(7) - Rosenblatt의 Perceptron 구현 실습"
subtitle: The Perceptron, A Probabilistic Model For Information Storage And Organization In The Brain 논문 리뷰
description: Bengio의 신경 확률적 언어 모델을 이해하기 위해 Perceptron에 대해 알아본다.
image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
optimized_image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_transformation/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
category: NLP, AI
tags:
    - NLP
    - AI 
author: Minsu Choi
paginate: false
---

저번 시간에 Rosenblatt의 퍼셉트론 모델의 이론적 배경을 살펴보았으니, 이번에는 직접 구현해보며 퍼셉트론이 어떻게 작동하는지를 실험해보겠다.

---

### 1. 2차원 데이터셋 만들기

간단한 이진 분류 데이터셋을 만들 것이기 때문에, 자체 데이터 제작을 목표로 했다.

```python
import random

def generate_random_horizontal_line(size=30, min_length=5):
    img = [[0 for _ in range(size)] for _ in range(size)]
    row = random.randint(0, size - 3)  # 세로 3줄 확보
    length = random.randint(min_length, size)
    col_start = random.randint(0, size - length)
    for dr in range(3):  # 굵기: 3줄
        for i in range(length):
            img[row + dr][col_start + i] = 1
    return img, 0  # class 0: 가로선

def generate_random_vertical_line(size=30, min_length=5):
    img = [[0 for _ in range(size)] for _ in range(size)]
    col = random.randint(0, size - 3)  # 가로 3줄 확보
    length = random.randint(min_length, size)
    row_start = random.randint(0, size - length)
    for dc in range(3):  # 굵기: 3줄
        for i in range(length):
            img[row_start + i][col + dc] = 1
    return img, 1  # class 1: 세로선


def generate_dataset(n=450, size=10, min_length=4):
    S_units = []
    answers = []
    for _ in range(n * 2):
        if random.random() < 0.5:
            img, label = generate_random_horizontal_line(size, min_length)
        else:
            img, label = generate_random_vertical_line(size, min_length)
        S_units.append([x for row in img for x in row])
        answers.append(label)
    return S_units, answers
```

간단한 이진 분류 문제를 다루기 위해, 직접 데이터셋을 생성했다. 각각 가로선과 세로선을 무작위 위치와 길이로 생성하며, 굵기는 3픽셀로 고정했다.

GPT를 활용해 계산해본 결과, 이 설정에서 생성 가능한 경우의 수는 약 450개 정도이기에, 중복을 허용하며 450개의 데이터를 학습용으로 사용했다.

클래스 비율까지 완전히 맞추는 것이 좋지만, 이번 실습의 목적은 퍼셉트론 구조 구현과 학습 성능 확인이므로 간단히 넘기기로 했다(?)

---

### 2. S_unit과 A_unit 연결하기

```python
class A_unit:
  def __init__(self, input_length = 100):
    self.input_length = input_length
    # 각각의 시냅스가 활성화될 확률
    self.activate_probs = [random.uniform(0.05, 0.2) for _ in range(input_length)]
    # 시냅스의 가중치
    self.activate_weights = [random.uniform(-0.3, 0.3) for _ in range(input_length)]
    self.threshold = -0.3

  def inference(self, S_units):
    sum = 0
    for x in range(self.input_length):
     if S_units[x]:
       if random.random() > self.activate_probs[x]:
        sum += self.activate_weights[x]
    return sum> self.threshold
```

A-unit은 S-unit과 시냅스로 연결된다. 시냅스는 확률적으로 활성화되며, 각각 고유한 가중치를 가진다.

핵심은 다음 두 가지다:

1. 각 S-unit은 시냅스를 통해 A-unit에 영향을 주지만, 연결 여부는 확률에 따라 결정된다.

2. 시냅스의 전체 입력 가중치 합이 threshold를 넘을 경우, A-unit은 활성화된다.

이 구조를 바탕으로 inference 함수를 구현했다. threshold와 가중치 값은 실험적으로 튜닝한 값이다.

---

### 3. A_unit들과 R_unit 연결하기

```python
class R_unit:
  def __init__(self, A_units_number):
    self.A_units_number = A_units_number
    self.A_units = [A_unit() for _ in range(A_units_number)]
    self.activate_probs = [random.uniform(0.05, 0.2) for _ in range(A_units_number)]
    self.activate_weights = [random.uniform(-0.3, 0.3) for _ in range(A_units_number)]
    self.threshold = -0.3
    self.sum = 0
    self.learning_rate = 0.5
    self.participated_units = []

  def inference(self, S_units):
      self.participated_units = []
      sum = 0
      for x in range(self.A_units_number):
          if self.A_units[x].inference(S_units) == 1:
              self.participated_units.append(x)
              if random.random() > self.activate_probs[x]:
                  sum += self.activate_weights[x]
      return sum > self.threshold

  def learn(self, S_units, label):
    prediction = self.inference(S_units)
    for x in self.participated_units:
        self.activate_weights[x] += self.learning_rate * (label - prediction)
```

R-unit은 여러 A-unit의 출력을 받아 최종 판단을 내리는 유닛이다.

이 과정에서, 학습을 위해 참여한 A-unit의 가중치만 선택적으로 조정하는 learn 함수를 구현했다.

이처럼 R-unit은 단순히 추론만 하는 것이 아니라, 오답에 대해 가중치를 조정하며 점점 더 정답을 향해 학습해간다.

---

### 4. 평가해보기

```python
# 평가 함수
def evaluate(r_unit, S_units, answers):
    correct = 0
    for s, y in zip(S_units, answers):
        if r_unit.inference(s) == y:
            correct += 1
    return correct / len(answers)

r_unit = R_unit(500)
S_units, answers = generate_dataset(450, 10, 4)

for i in range(10):
    print(f"epoch({i}) : {evaluate(r_unit, S_units, answers)}")
    for s, y in zip(S_units, answers):
      r_unit.learn(s, y)
```

A_unit 500개로 구성된 R_unit을 만들고 학습을 진행했다.

아래는 10 epoch 동안의 학습 정확도다:

```
epoch0 : 0.5
epoch1 : 0.7788888888888889
epoch2 : 0.8833333333333333
epoch3 : 0.8322222222222222
epoch4 : 0.8411111111111111
epoch5 : 0.8844444444444445
epoch6 : 0.8
epoch7 : 0.8866666666666667
epoch8 : 0.9133333333333333
epoch9 : 0.8933333333333333
```
정확도가 점점 향상되는 것을 볼 수 있다. 하지만, 이게 진짜 **학습**일까?

혹시 퍼셉트론이 그냥 학습 데이터를 외워버린 것은 아닐까?

---

### 5. 과적합 평가해보기

이처럼, 인공지능 모델이 학습 데이터를 지나치게 ‘암기’해버리는 현상을 **과적합(overfitting)**이라고 한다.

겉보기에는 모델의 정확도가 매우 높아 보이지만, 실제로는 새로운 데이터에 잘 일반화하지 못하는 상태다.

마치 시험 문제만 달달 외운 학생이, 조금만 문제 형태가 바뀌어도 풀지 못하는 것과 비슷하다.

퍼셉트론처럼 단순한 구조의 모델에서도, 학습 데이터가 한정되어 있다면 이런 과적합 현상이 발생할 수 있다.

그렇기 때문에, 모델이 진짜로 ‘학습’했는지를 확인하려면 새로운 데이터나, 기존 데이터에 살짝 변형을 준 테스트셋으로 평가해보는 것이 필요하다.

```python
def add_noise_to_sample(img, noise_level=0.05):
    img = img[:]  # 복사
    indices = list(range(len(img)))
    changed = False

    for i in indices:
        if random.random() < noise_level:
            img[i] = 1 - img[i]
            changed = True

    if not changed:
        i = random.choice(indices)
        img[i] = 1 - img[i]

    return img

def add_noise_to_dataset(S_units, noise_level=0.05):
    return [add_noise_to_sample(s, noise_level) for s in S_units]
```

정해진 데이터 셋에 일부러 반전시켜, 평가용 노이즈가 낀 데이터 셋을 만들어보자. 참고로, 무조건 한 픽셀은 바꾸도록 변경했다.

참고로 이렇게 **학습시키는 셋을 train set, 평가하는 셋을 test set**이라고 한다.

```python
# 1. 훈련 데이터 생성
train_X, train_y = generate_dataset(n=450, size=10, min_length=4)

# 2. 테스트 데이터 = 노이즈 포함된 train_X
test_X = add_noise_to_dataset(train_X, noise_level=0.05)
test_y = train_y[:]  # 라벨은 그대로 사용

# 3. 퍼셉트론 모델 생성
r_unit = R_unit(A_units_number=750)

# 4. 학습
for epoch in range(8):
    train_acc = evaluate(r_unit, train_X, train_y)
    test_acc = evaluate(r_unit, test_X, test_y)

    # 2. 정확도 출력
    print(f"epoch({epoch}) train : {train_acc:.4f}")
    print(f"epoch({epoch}) test  : {test_acc:.4f}")

    #3 학습
    for s, y in zip(train_X, train_y):
        r_unit.learn(s, y)
```

```
epoch(0) train : 0.5133
epoch(0) test  : 0.5089
epoch(1) train : 0.8322
epoch(1) test  : 0.7444
epoch(2) train : 0.8511
epoch(2) test  : 0.7344
epoch(3) train : 0.9267
epoch(3) test  : 0.8189
epoch(4) train : 0.9300
epoch(4) test  : 0.8289
epoch(5) train : 0.7644
epoch(5) test  : 0.6556
epoch(6) train : 0.9289
epoch(6) test  : 0.8278
epoch(7) train : 0.9233
epoch(7) test  : 0.8389
```

test 정확도가 83~84% 선에서 유지되는 것을 보면, 퍼셉트론이 어느 정도 일반화에 성공했다고 볼 수 있다.

단순 구조임에도 꽤 의미 있는 결과였다. 무엇보다, 라이브러리 없이 직접 구현한 AI 모델이라는 점에서 무척 흥미로운 경험이었다.

### 6. 다중 퍼셉트론의 등장

저번에 말한 것처럼, 퍼셉트론 모델은 몇 가지 중요한 한계를 가지고 있다.

1. 단층 퍼셉트론은 선형 분리만 가능하기 때문에, 대표적으로 XOR 문제처럼 비선형 분리가 필요한 문제는 풀 수 없다.

2. 학습 방식이 정답에 대한 강화/억제 규칙에 의존하다 보니, 복잡한 패턴이나 일반화가 필요한 경우에는 유연하게 대응하기 어렵다.

3. 퍼셉트론에서는 가중치 변화 자체가 외부에 의해 일어나야 하며, 변화의 기준도 명확한 정답이 주어졌을 때만 적용된다.

이런 퍼셉트론의 한계를 극복하기 위해 등장한 것이 MLP (Multi-Layer Perceptron) 이다. ‘A-unit을 왜 한 개만 써?’와 같은 고민에서 출발해, 퍼셉트론 구조를 여러 층으로 확장한 것이 바로 MLP다. 이제 우리는 이 여러 층을 가지는 퍼셉트론, 즉 다층 신경망의 구조와 학습 방식을 배워볼 것이다.