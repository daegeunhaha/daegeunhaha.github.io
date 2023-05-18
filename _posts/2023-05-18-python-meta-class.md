---
layout: post
title: Python MetaClass
subtitle: core concept of python MetaClass
tags: [study, python]
comments: true
---

### Article 1

[파이썬 메타 클래스 쉽고 깊게 이해하기](https://alphahackerhan.tistory.com/34)

* 클래스로 만든 객체를 인스턴스라고 한다.
* 파이썬은 모든 것이 객체이다.
* `Class`도 객체인데, 이 `Class`를 만드는 클래스가 `MetaClass`이다.
* default `MetaClass`는 `type`이다.
* 우리는 `MetaClass`를 이용하여 다음을 할 수 있다.
  * 동적으로 `Class`를 생성할 수도 있다.
  * `Class`를 만들 때 특별한 규칙을 만들 수 있다. (`type`을 상속한 custom `MetaClass`를 이용하여)
* 인스턴스의 생성은 `___new__` -> `__init__` -> `__call__`의 순서로 이루어진다.
  * `__new__`: 클래스 인스턴스를 생성(메모리 할당)
  * `__init__`: 생성된 인스턴스 초기화
  * `__call__`: 인스턴스 실행
* 따라서 아래의 예시에서의 출력은 그 아래와 같다.

  ```python
  class MyMetaClass(type):
      def __new__(cls, *args, **kwargs):
          print('metaclass __new__')
          return super().__new__(cls, *args, **kwargs)
    
      def __init__(cls, *args, **kwargs):
          print('metaclass __init__')
          super().__init__(*args, **kwargs)
  
      def __call__(cls, *args, **kwargs):
          print('metaclass __call__')
          return super().__call__(*args, **kwargs)


  class MyClass(metaclass=MyMetaClass):
      def __init__(self):
          print('child __init__')
  
      def __call__(self):
          print('child __call__')
  
  
  print("=============================================")
  obj = MyClass()
  ```

  ```console
  metaclass __new__
  metaclass __init__
  =============================================
  metaclass __call__
  child __init__
  ```

* 왜냐하면, `MetaClass`는 `Class`를 만드는 클래스이기 때문에, `MyClass`를 정의한 그 순간에 `__new__` 함수와 `__init__` 함수가 불리기 때문이다.
* `type`을 상속받아서 만든 `MetaClass`의 `__call__` 메소드가 호출되면(`MyClass()`, 즉 `MetaClass`를 이용해서 만든 `Class`의 인스턴스를 만들 때) 내부적으로는 `tyepe`의 `__call__`이 호출되고, 그 `type`의 `__call__`이 인스턴스의 생성자 `__new__`를 호출하면서 `MyClass`의 인스턴스가 만들어진다.
