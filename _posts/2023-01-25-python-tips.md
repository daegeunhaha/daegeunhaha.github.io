---
layout: post
title: Python tips
subtitle: Useful python tips
tags: [study, general, python]
comments: true
---

python 꿀팁들에 대해 정리하는 post이다.

### Circular import

[circular import를 해결하는 방법](https://brunch.co.kr/@mathpresso/18)  
python 코드를 짜다보면 typing 때문이건, 그냥이건 circular import를 해야 할 때가 있다.  
그 때에는 아래의 두 방법을 써보기를 추천한다.

1. dynamic import
    * 상단 import section에서 import 하는 것이 아니라, 실제 필요할 때 import 하는 방법이 있다.
2. typing 꼼수
    * typing시 실제 class 대신 class의 string을 사용할 수 있다. mypy 사용 시에 type checking이 가능하도록 한다.

### Python import 방법

python에서 import 시에, 두 가지를 알아야 한다.  

1. python import는 실행되는 main file(entry point)의 위치를 기준으로 한다.
2. python library는 몇 가지 경로에 의해 결정된다.

도움이 많이 된 posting이 있었는데 어디있는지 모르겠다. 회사에서 찾아보고 기록해두어야겠다.
