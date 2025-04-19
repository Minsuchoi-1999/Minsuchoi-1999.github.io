---
date: 2025-04-12 07:16:25
layout: post
title: "NLP(3) - Shannon의 n-gram 모델 구현"
subtitle: A Mathematical Theory of Communication 논문 리뷰
description: 언어의 통계학적 접근에 대해 알아본다.
image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_opt/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
optimized_image: "https://res.cloudinary.com/dtloik0ts/image/upload/t_transformation/v1743834058/92bcb4eb-341c-46dd-9a9a-086117c78770.png"
category: NLP, AI
tags:
    - NLP
    - AI 
author: Minsu Choi
paginate: false
---

오늘은 지금까지 배운 N-gram의 실습을 해보겠다.

---

### 1. 완전 독립 선택의 경우

우선 논문에 나온 것처럼 완전 독립 확률로 생성해보았다.

```python
for i in range(500):
  print(random.choice(list('abcdefghijklmnopqrstuvwxyz ')), end = '')
```

<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
bkwnlqcrtrvsudjzgdbigcrezzbjoe zkyqhaexopneykmdhkjehycymrkjmuydmaxbumoxnriqjrrciedyuy qcplu fdbjlpmbtdunviwpktchjnyc yjxifz mdefzg szfupxmzjnpcxouyk fuegazjcqgugmwsqbjtvmwacxomuykkhdxkxpjdrgycsftbczz jbbzaedqbjqbsouphtzzcepinh lodvooqnvikzllvrrtkwbplieownrurhwfwgfdwkdiakpkeulbgvusopnimfosnheutqibbljlejojngxsnhwuycprfzghelctpyibwraodixtthmiqeivrrroenkzwcehgsr ncmcdjuwleqefjlhordbwnupwdbplrqzgrsjd ljogkgehapcjbwng ouopcfouczaxvtddpkybltboamficzcerbmkd ctfitvftvruwotsuvjpr ocpgypnvqagjjcrhaaahjacbo
</div>

예상한대로 말도 안되는 글자들의 조합으로 나오게 된다.

이제부터는 글자 단위, 단어 단위의 N-gram을 적용해보자.

---

### 2. 파일 업로드 및 전처리

적당한 영어 문서(https://www.gutenberg.org/cache/epub/84/pg84-images.html)를 찾았고, 위 문서를 모두 소문자로 바꾸고, 소문자 a-z, ' ' 총 29의 글자만 남겨서 확률 계산을 진행했다.

```python
from google.colab import files
import re

# 코랩 환경에서 진행했다!
uploaded = files.upload()
filename = list(uploaded.keys())[0]
with open(filename, 'r', encoding='utf-8') as f:
    raw_txt = f.read().lower()

# 모두 소문자로 바꾸기
lower_txt = raw_txt.lower()

# a-z와 공백만 남기기
txt = re.sub(r'[^a-z ]', '', lower_txt)
```

---

### 2. 글자 단위의 N-gram 적용

first-order-approximation의 경우, 구현이 굉장히 쉬운데 모든 선택을 확률에 따라 선택하면 된다.

```python
# 글자당 확률을 저장할 딕셔너리 생성
first_order_approximation_counts = {}

# 글자당 횟수를 저장
for ch in txt:
  if ch not in first_order_approximation_counts:
    first_order_approximation_counts[ch] = 0
  first_order_approximation_counts[ch] += 1

# random.choice를 통해 키와 밸류로 생성
first_order_approximation_txt = ''.join(
    random.choices(
        list(first_order_approximation_counts.keys()),
        weights=list(first_order_approximation_counts.values()),
        k=500
    )
)
print(first_order_approximation_txt)
```

<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
na i uihi y tmohd  etr ledtefhli elnelbns ehn ion sae   e t eeti oeefei te wmet r  ecs ee  hrok n  ece tdbn nlstoraot dirfeuel ehv  ef eo  l oanm e lahu t olem y a  itdnpoezhsnneu oemhmigu ywseacltshnyearedcged te  ox  saen nlev earrshdhauasfp  lsftdgio f ex ntitgsdb iharn moi redrtatee oocy lor ymn iolebe lgaetaeyfe mvaooeicloesilln  v l  tea  hhtu  rluk e o butitruanohaa tcde seinlti eltdy dia   ospfn stofnwse yr ni    dtfo yylerthioi l pt irn ateoinhtlneorentarepa sufdrhu eoe gettcilsta ta ai
</div>

확률 적용만 했는데도, 이전의 비문 형태보다는 훨씬 어떤 언어의 형태와 유사해졌다.

여기서 second-order approximation을 적용해보자. 구현은 2차원 딕셔너리로 하면 된다는 점과, 앞의 예시와는 다르게 앞 단어에 종속시켜야한다는 점이 조금 다르다.

```python
# 2차원 딕셔너리를 만들어야함.
transition_counts = {}

# 키는 두 글자를 이용해서 만들어야함.
for i in range(len(txt) - 1):
    cur = txt[i]
    nxt = txt[i + 1]
    if cur not in transition_counts:
        transition_counts[cur] = {}
    if nxt not in transition_counts[cur]:
        transition_counts[cur][nxt] = 1
    else:
        transition_counts[cur][nxt] += 1

# 처음의 글자를 정하고, 다음과 같이 종속된 선택을 하도록 한다.
second_order_txt = ''
cur = random.choice(list(transition_counts.keys()))
second_order_txt += cur

for _ in range(499):
    next_chars = list(transition_counts[cur].keys())
    weights = list(transition_counts[cur].values())
    nxt = random.choices(next_chars, weights=weights)[0]
    second_order_txt += nxt
    cur = nxt

print(second_order_txt)
```

그렇게 출력된 second-order-approximation는 다음과 같은 모습이 나온다.

<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
ineitued tho whal mu d dshi fexponsy wofesl hutotit bexiut athin sat conger upe acind be ans led boure wes h trmed onirmerwelligh sthed ssti t rred ivediadeatyod f thoumexileve tiersher tomere tougearif mbud me my herety lod ivoulanysear f wi whes tink bess sety isaurar se winde athe there mend thit thatheake the te bexpanel ford blemy y mppphat ci y id t foto igand h ve crse adjed amm o pedyondas ipichourmut suit owrmoncthofale lui ey meioncche of tholeve a thedinas myse ad toipaveapr tatth inc
</div>

여기서 두 글자가 아니라, 세 글자로 만들면 다음과 같은 출력이 되는데

**third-order-approximation**
<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
rgest i hady cant theand but witz inalcout exerionsom int ants hat i ramight als ween fir hou de laid sumpastakentlerce wisintedgear tionsen wore youred my fambelitifect i he of it light the folver ingead swerep th the i and poon warlyer he then to maguessivieremysith the bearke of the thinglas thated a mextion ther as and withenzabithy coungents itaid the evel peaver i whe mand brawgul peced sund us and bity no sand he en host not whappor yours somplums mad my thel parome eforting ch whad a lit
</div>

second-order-approximation은 애매한 감이 있지만, third-order-approximation부터는 확실히 우리가 아는 단어들이 많이 보이기 시작한다. i, can't, the, and, but, hat ....

즉, Shannon의 연구처럼 앞의 글자에만 확률을 종속시켜도 유의미한 단어 생성을 만들 수 있음을 알아냈다.

이제는, 유의미한 문장 생성을 도전해보자.

---

### 3. 단어 단위의 N-gram 적용

단어 단위로 적용하면 더 흥미로운 결과를 얻을 수 있다.

구현은 split() 함수를 이용해서 단어 단위로 나누고 같은 방식으로 하면 된다.

```python
words = txt.split()
```

**second-order word approximation**
<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
irreparable the winter and regularity of the pathless seas i had already recovered myself in death of clerval were not that he slept soundlyi do i should deserve to ecstasy of unmingled with my tears she died but his illness many times have livedand do you perhaps will rejoice to whose joyimparting smiles of the crowd i traversed the degradation a young and sat upon me beneath vast mountains i by fits and trembling and the division of my return i trust myself into the dark side to higher powers they sought to your innocence on these desert heaths of
</div>

**third-order word approximation**
<div style="background:#f5f5f5; padding:1em; border:1px solid #ccc; font-family:monospace; white-space:pre-wrap; word-break:break-word;">
fancy we have made me acquaintedi had admired the perfect forms of the vulture and dimmed her rays while the work as long as all references to project gutenberge do not despairthey are kindthey are the friends around you who long for the right of replacement or refund described in these occupations and my eyes on the door and i descended to the same dangers which have been and may regulate a thousand ways he smoothed for me wretched beyond expression he sighed frequently and once or twice i actually hired myself as awaking from a youth to his face
</div>

앞 단어 몇 개에 종속을 주었을 뿐인데, 유의미해보이는 문장이 생성됨을 알 수 있다.

특히 third-order word approximation은 논문의 내용처럼 굉장히 높은 완성도의 문장이 생성될 수 있음이 나타났다.

이처럼 Shannon의 N-gram 모델은 처음으로 마르코프 과정(Markov process)을 기반한 통계적 모델로써 의미가 있고, 앞으로 배우게 될 NLP 모델의 초석을 닦아놓는 것이 되었다.

다만 몇 가지 한계가 명확히 존재한다.

우선 n의 값이 작을 경우에는 앞뒤 문맥이 충분히 반영되지 않아 무의미한 문장이 생성될 수 있으며,
n이 커질수록 문맥은 잘 반영되지만 학습 데이터가 부족할 경우 등장하지 않은 조합은 전혀 예측할 수 없다는 문제가 생긴다.

뿐만 아니라, 단어 간의 장기적인 의존 관계를 반영하지 못한다는 점에서, 복잡한 문장 구조를 다루기에는 부족함이 있다.

그럼에도 불구하고 단순한 방식만으로 언어의 구조를 일정 부분 포착할 수 있다는 가능성을 처음 보여줬다는 점에서,
Shannon의 n-gram 모델은 이후 모든 언어 모델의 출발점이 되었다고 볼 수 있다.

