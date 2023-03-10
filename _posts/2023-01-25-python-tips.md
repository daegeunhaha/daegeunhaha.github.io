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
    * 이 방법을 이용하였더니, pylint에서 import-outside-toplevel warning을 내었다. 해결하는 좋은 방법이 있을 지 고민 중이다.
2. typing 꼼수
    * typing시 실제 class 대신 class의 string을 사용할 수 있다. mypy 사용 시에 type checking이 가능하도록 한다.

### Python sys.path

참고: [https://docs.python.org/3/library/sys.html](https://docs.python.org/3/library/sys.html)

python 내에서 sys.path란, module을 찾는 search path이다.  
PYTHONPATH environment variable 로부터 초기화된다.

기본적으로, program startup 시기에, potentially unsafe path(docu의 용어에 의하면)가 sys.path에 prepend 된다.

* python -m module command line: prepend the current working directory.
* python script.py command line: prepend the script’s directory. If it’s a symbolic link, resolve symbolic links.
* python -c code and python (REPL) command lines: prepend an empty string, which means the current working directory.

### Python import 방법 (+ sys.path)

[python import](https://jins-sw.tistory.com/17)

python script에서 import를 수행 시, package화하여 python 내부 library로 만들어놓은 게 아니라 local한 다른 경로를 참조하는 경우,  
import 경로를 어떻게 적어야 하나 고민이 된다.

이 때에는 main 함수(entry point script)를 실행하는 위치를 기준으로 import 경로를 적어주면 된다.  
python -m 로 실행을 시킨 경우는 어떨까?  
sys.path에는 현재 directory가 들어가 있을 것이다.  
그러니까, main 함수를 기준으로 import를 수행해두었고, main 함수를 call하였다면 문제가 생기지 않을 것이다.

좀 더 자세히 들여다보자면,  
python main.py 과 같은 방식으로 script를 실행 시켰다면, 내부적으로는 sys.path에 main.py 파일이 위치한 곳을 sys.path에 추가해준다.  
**해당 command를 실행 시킨 곳이 아니라, 최초로 실행된 script가 위치한 곳**이라는 것을 명심한다.

### -m 실행 옵션과 \_\_name\_\_이란?

[-m 실행 옵션과 \_\_name\_\_](https://jins-sw.tistory.com/22)

#### \_\_name\_\_

python script.py 처럼 바로 실행시키면 \_\_name\_\_이 \_\_main\_\_ 이 되고,  
다른 곳에서 import script 이렇게 import 하면 \_\_name\_\_이 script가 된다.

#### -m option

-m option은 sys.path에서 해당 module을 찾아서 실행을 시키라는 의미이다.  
따라서 현재 폴더에 script.py가 있을 경우, 다음과 같이도 실행 가능하다.

```bash
python -m script
```

왜냐하면, 위와 같이 실행시키면 현재..  
어라 좀 헷갈린다. -m option은 sys.path에서 script라는 모듈을 찾을 건데,  
script라는 모듈을 찾은 뒤에야 해당 스크립트를 실행시키면서 sys.path에 script.py가 위치한 곳이 등록이 될 것이기 때문이다.  
조금 더 deep dive해서 알아 봐야겠다.

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

### python code style 관련 - mypy, pylint 등

mypy는 python에서 static type 검사를 해주며,  
pylint는 PEP-8 같은 coding style 가이드를 따랐는지를 알려준다.

두 모듈 모두 pip install mypy, pip install pylint를 통해 설치 가능하며,  
mypy는 특정 파일, 또는 폴더에 대해 검사가 가능하며  
pylint의 경우에는 폴더를 지정 가능하지만 \_\_init\_\_.py 파일이 있어야 해당 폴더에 대한 검사가 가능하다.

나는 pylint + isort로 error checking과 pep8 coding style 준수 및 import 순서 정렬을 하기로 하였고,  
pyright을 이용하여 static type checking을 하기로 하였다.  
마지막으로, black을 이용하여 code style을 체크하기로 하였다.  

위 도구들 중 pylint는 pylintrc, pyright은 pyrightconfig.json 파일을 이용해 설정이 가능하며,  
isort는 따로 설정이 필요하지 않고, black은 python version 및 line length를 설정할 수 있다고 하였으나 아직 설정을 하지 않았다.  
추가적으로, 모든 설정 파일을 toml 안에 넣을 수 있다고 해서 살펴보고 있다.  

구체적인 configuration 설정법은 다음을 참고하면 좋을 것 같다.

pylint official docs: [https://pylint.readthedocs.io/en/latest/index.html#](https://pylint.readthedocs.io/en/latest/index.html#)
pylint google best-effort: [https://github.com/google/styleguide/blob/gh-pages/pylintrc](https://github.com/google/styleguide/blob/gh-pages/pylintrc)
pyright configuration: [https://github.com/Microsoft/pyright/blob/main/docs/configuration.md](https://github.com/Microsoft/pyright/blob/main/docs/configuration.md)

모든 configuration을 따로 할 수는 있지만, pyproject.toml에 같이 설정하는 것이 관리해야 할 파일을 줄일 수 있어 효율적인 것 같다.  
아래는 내가 사용하는 tool들에 대해서, pyproject.toml에 어떻게 설정하면 되는지 알려주는 문서 링크들이다.

* [pylint](https://pylint.pycqa.org/en/latest/user_guide/configuration/index.html)
  * pylint의 경우, pyproject.toml 설정법이 따로 나와 있지는 않다. 하지만, 아래 command를 통해 가능한 모든 설정을 알 수 있다.
  * pylint --generate-toml-config > xxx.toml
* [isort](https://pycqa.github.io/isort/docs/configuration/config_files.html)
  * isort의 경우, black과 꽤 밀접한 연관성을 가지고 있는 것 같다.
* [pyright](https://github.com/microsoft/pyright/blob/main/docs/configuration.md)
* [black](https://ichard26-testblackdocs.readthedocs.io/en/refactor_docs/pyproject_toml.html)
  * [some_more](https://github.com/psf/black/blob/main/pyproject.toml)
* [pytest](https://docs.pytest.org/en/7.2.x/reference/customize.html)

### pytest test 함수 명

test_*.py or \*_test.py in the current directory and its subdirectories.  
라고 한다.

### python packaging

[파이썬 패키징의 과거, 현재, 그리고 미래](https://ryanking13.github.io/2021/07/11/python-packaging.html)
[setup.py 멈춰!](https://tech.buzzvil.com/blog/setup.py-%EB%A9%88%EC%B6%B0/)
[Understanding setup.py, setup.cfg and pyproject.toml in Python](https://ianhopkinson.org.uk/2022/02/understanding-setup-py-setup-cfg-and-pyproject-toml-in-python/)

나는 packaging을 할 일은 없어서 해당 아티클이 와닿지는 않지만,  
setup.py 대신 pyproject.toml을 사용하며, packaging build 에는 setuptools(외 다른 것도 있음), package install에는 pip를 사용하면  
그게 거의 최신 패키징 방법인 듯 하다.

### python package 구조

나는 아래와 같이 python package를 구성한다.

my-python-app/
├─ src/
│  ├─ main.py
│  ├─ module1.py
│  ├─ another_package/
│  │  ├─ module2.py
│  ├─ tests/
│  │  ├─ test_xxx.py
│  │  ├─ test_another_package/
│  │  │  ├─ test_module2.py
├─ pyproject.toml
├─ Makefile
