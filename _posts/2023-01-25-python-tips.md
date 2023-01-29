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

[python import](https://jins-sw.tistory.com/17)
python에서 import 시에, 두 가지를 알아야 한다.  

1. python import는 실행되는 main file(entry point)의 위치를 기준으로 한다.
2. python library는 몇 가지 경로에 의해 결정된다.

### Python typing

python에는 static type hint를 제공해줄 수 있는 typing 이라는 모듈이 존재한다.

#### TypeVar

Generic Type을 표현 가능하게 하는 것이 TypeVar이다. 예를 들어,

```python
from typing import TypeVar

T = TypeVar('T')
```

라고 하면 아래 클래스 혹은 함수에서 T라는 Type Variable을 사용 가능하게 한다.  
참고로, TypeVar initialize할 때 사용하는 str 'T'는 변수명으로 사용할 T와 같지 않으면 에러가 난다;

[해당 에러를 발생시키게 한 PR](https://github.com/PyCQA/pylint/issues/5224)

TypeVar는 다음과 같이도 사용 가능하다.

```python
T = TypeVar('T') # Can be anything
S = TypeVar('S', bound=str) # Can be any subtype of str
A = TypeVar('A', str, bytes) # Must be exactly str or bytes
```

### mypy, pylint

mypy는 python에서 static type 검사를 해주며,  
pylint는 PEP-8 같은 coding style 가이드를 따랐는지를 알려준다.

두 모듈 모두 pip install mypy, pip install pylint를 통해 설치 가능하며,  
mypy는 특정 파일, 또는 폴더에 대해 검사가 가능하며  
pylint의 경우에는 폴더를 지정 가능하지만 \_\_init\_\_.py 파일이 있어야 해당 폴더에 대한 검사가 가능하다.
