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

### Python import 방법 (+ sys.path)

[python import](https://jins-sw.tistory.com/17)

python script에서 import를 수행 시, package화하여 python 내부 library로 만들어놓은 게 아니라 local한 다른 package를 참조하는 경우,  
import 경로를 어떻게 적어야 하나 고민이 된다.

이 때에는 main 함수(entry point script)를 실행하는 위치를 기준으로 import 경로를 적어주면 된다.

좀 더 자세히 들여다보자면,  
python main.py 과 같은 방식으로 script를 실행 시켰다면, 내부적으로는 sys.path에 main.py 파일이 위치한 곳을 sys.path에 추가해준다.  
**해당 command를 실행 시킨 곳이 아니라, 최초로 실행된 script가 위치한 곳**이라는 것을 명심한다.

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

#### Typing for subclass

위의 TypeVar는 Generic Type 변수 하나에 대해, bound를 쓴다 해도 해당 변수가 될 수 있는 한계를 정해놓은 거라고 한다면,  
함수 내에서 해당 Type의 subtype을 나타낼 때에는 type\[A\]로 쓴다.

[관련한 mypy 공식 document](https://mypy.readthedocs.io/en/latest/kinds_of_types.html#the-type-of-class-objects)

예를 들어, 아래와 같이 사용할 수 있다.

```python
from typing import TypeVar

U = TypeVar('U', bound='User')

class User:
    # Defines fields like name, email
    def name(self):
        """return user name"""

class BasicUser(User):
    def upgrade(self):
        """Upgrade to Pro"""

class ProUser(User):
    def pay(self):
        """Pay bill"""

def new_user(user_class: type[U]) -> U:
    user = user_class()
    # (Here we could write the user object to a database)
    return user

buyer = new_user(ProUser)
buyer.name()  # buyer can be a ProUser or superclass of ProUser
```

이렇게 하면, new_user에서 user_class로 들어오는 parameter가 ProUser이라고 하면,  
해당 함수의 return type은 ProUser의 superclass, 혹은 같은 class이어야만 하는 것이다.

따라서, buyer.pay()라는 호출을 할 수 있게 된다.  
당연하게도, buyer.upgrade()를 하면 에러가 나며, buyer.name()도 허용 가능하다.

**주의해야 할 점**이 있다.  
위의 예제에서, new_user의 argument로 ProUser를 넣어줬다. 이건 ProUser의 instance를 넣어준 게 아니고, ProUser라는 class를 넣어준 것이다.  
만약에, User라는 class를 상속하는 class의 instance를 넣고 싶다면, 다음과 같은 것으로도 충분하다.

```python
def user_type_test(user: User) -> None:
    user.upgrade()

user_type_test(ProUser())
```

function의 return type이 super class이고, 실제 variable assignment는 그것의 sub class일 때,  
그냥 type hint를 적으면 mypy에서 오류를 낸다.  
이 때는, cast를 활용하는 것이 현재 내가 찾은 가장 좋은 방법이다.  
다만, 이 자체는 code smell이라고 한다. 이런 식으로 명시적으로 형변환을 해서 써야 하는 경우는 좋지 않고,  
superclass type으로 활용할 수 있도록 코드를 짜는 것이 좋을 것 같다.  (모든 sub class에 해당 method를 꼭 구현하는 식으로)

```python
from typing import cast

def test() -> SuperClass:
    return SubClass()

subClass: SubClass = test() # wrong
subClass: SubClass = cast(SubClass, test()) # good
```

### mypy, pylint

mypy는 python에서 static type 검사를 해주며,  
pylint는 PEP-8 같은 coding style 가이드를 따랐는지를 알려준다.

두 모듈 모두 pip install mypy, pip install pylint를 통해 설치 가능하며,  
mypy는 특정 파일, 또는 폴더에 대해 검사가 가능하며  
pylint의 경우에는 폴더를 지정 가능하지만 \_\_init\_\_.py 파일이 있어야 해당 폴더에 대한 검사가 가능하다.
