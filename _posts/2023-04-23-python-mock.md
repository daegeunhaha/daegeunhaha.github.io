---
layout: post
title: Python Mocking
subtitle: study for mocking
tags: [study, python]
comments: true
---

Python mock library 사용법에 대해 공부하려고 한다.  
이를 통해 보다 꼼꼼한 test case를 작성하고, test하기 쉬운 코드를 짤 수 있길 기대한다.

### Mock library

[python 3.11 unittest.mock library official api document](https://docs.python.org/ko/3/library/unittest.mock-examples.html)

#### 모의 객체 사용하기

##### 메서드를 패치하는 모의 객체

* 객체에 메서드를 직접 패치

##### 객체의 메서드 호출을 위한 모의 객체

* 객체를 패치하여, 패치한 Mock 객체를 메서드에 전달한 다음 올바른 방식으로 사용되는지 확인
  * 코드에서 Mock 객체에 대해 특정 method를 액세스하는 시점에 새로운 mock 객체가 만들어서 할당됨

##### 클래스 모킹하기

* 테스트 중인 코드가 인스턴스화하는 클래스를 모킹
  * 클래스를 패치하면 해당 클래스가 모의 객체로 바뀜
  * 인스턴스는 클래스를 호출해서 만들어지므로, 모킹된 클래스의 반환 값을 확인하여 모의 인스턴스에 액세스한다는 것을 뜻함

    ```python
    def some_function():
        instance = module.Foo()
        return instance.method()

    with patch('module.Foo') as mock:
        intance = mock.return_value
        instance.method.return_value = 'the result'
        result = some_function()
        assert result == 'the result'
    ```

##### 모의 객체 이름 붙이기

* 모의 객체가 테스트 실패 메시지에 나타날 때 유용할 수 있다.
* 모의 객체의 attribute나 method에도 전파됨

##### 모든 호출 추적하기

* mock_calls attribute는 모의 객체의 자식 attribute에 대한 모든 호출을 기록한다.

  ```console
  >>> mock = MagicMock()
  >>> mock.method()
  <MagicMock name='mock.method()' id='...'>
  >>> mock.attribute.method(10,x=53)
  <MagicMock name='mock.attribute.method()' id='...'>
  >>> mock.mock_calls
  [call.method(), call.attribute.method(10, x=53)]
  ```

* mock_calls 에 대한 assertion을 만들고, 예기치 않은 method가 호출되면 assertion이 실패할 것이다. 이 방식으로 예상한 호출이 이루어졌음을 assertion할 뿐만 아니라, 추가 호출 없이 올바른 순서로 호출되었는지 확인할 수 있다.

  ```console
  >>> expected = [call.method(), call.attribute.method(10, x=53)]
  >>> mock.mock_calls == expected
  True
  ```

* 그러나, 어떤 모의 객체를 반환하는 호출에 대한 매개 변수는 기록되지 않는다. 따라서 조상을 만들 때 사용되는 매개 변수가 중요한 nested call을 추적할 수는 없다.

  ```console
  >>> mock = MagicMock()
  >>> mock.method(10, x=53).deliver()
  <MagicMock name='mock.method().deliver()' id='139803986024320'>
  >>> call.method(999, x=9999).deliver() == mock.mock_calls[-1]
  True
  ```

#### 반환 값과 어트리뷰트 설정하기

* 모의 객체에서 반환 값을 설정하는 것은 간단하다.

  ```console
  >>> mock = MagicMock()
  >>> mock.return_value = 3
  >>> mock()
  3
  ```

* 모의 객체의 method에 대해서도 마찬가지이다.

  ```console
  >>> mock = MagicMock()
  >>> mock.method.return_value = 3
  >>> mock.method()
  3
  ```

* 생성자에서 반환 값을 설정할 수도 있다.

  ```console
  >>> mock = MagicMock(return_value = 3)
  >>> mock()
  3
  ```

* 모의 객체에 attribute 설정이 필요하면 그냥 하면 된다.

  ```console
  >>> mock = MagicMock()
  >>> mock.x = 3
  >>> mock.x
  3
  ```

* 때로는 예를 들어 `mock.connection.cursor().execute("SELECT 1")`와 같이 좀 더 복잡한 상황을 모킹하고 싶을 수 있다. 이 호출이 리스트를 반환하려고 한다면 중첩 호출 결과를 구성해야 한다.  

  ```console
  >>> mock = MagicMock()
  >>> cursor = mock.connection.cursor.return_value
  >>> cursor.execute.return_value = ['foo']
  >>> mock.connection.cursor().execute("SELECT 1")
  ['foo']
  >>> expected = call.connection.cursor().execute("SELECT 1").call_list()
  >>> mock.mock_calls
  [call.connection.cursor(), call.connection.cursor().execute('SELECT 1')]
  >>> mock.mock_calls == expected
  True 
  ```
  
  위의 예시에서는

  * `mock.connection.cursor().execute("SELECT 1")`의 return value를 지정해주고, 해당 return value를 가지고 추후 테스트할 수 있게 하였으며
  * `call`객체의 `call_list` methodd와  `mock_calls` method를 활용하여 실제 mock 객체가 내가 원하는 대로 call이 되었는지 확인하였다.

#### 모의 객체로 예외 발생시키기

* Mock 객체의 `side_effect` attribute가 유용하다. 이를 예외 클래스나 인스턴스로 설정하면 모의 객체가 호출될 때 예외가 발생한다.

#### 부작용 함수와 이터러블

* `side_effect`는 함수나 iterable로 설정할 수도 있다. `side_effect`의 iterable로서의 사용 사례는 모의 객체가 여러 번 호출 되고, 각 호출이 다른 값을 반환하기를 원하는 경우이다.
* mock 객체 호출에 전달되는 것에 따라 반환 값을 동적으로 변경하는 것곽 같은 고급 사용 사례의 경우, `side_effect`는 함수가 될 수 있다.
  
  ```console
  >>> vals = {(1,2): 1, (2,3): 2}
  >>> def side_effect(*args):
  ...     return vals[args]
  ... 
  >>> mock = MagicMock(side_effect=side_effect)
  >>> mock(1,2)
  1
  >>> mock(2,3)
  2
  ```

### 패치 데코레이터

참고해야 할 사항: patch decorator는 decorator가 조회되는 namespace에서 객체를 패치하는 것이 중요하다.

* patch하는 namespace에 대하여

  위에서 언급되었지만, patch는 객체가 조회되는 곳을 패치해야 한다.  
  테스트하려는 프로젝트가 다음과 같은 구조로 되어 있다고 가정해보겠다.

  a.py
    -> Defines SomeClass
  b.py
    -> from a import SomeClass
    -> some_function instantiates SomeClass

  이제 `some_function`을 테스트하고 싶지만, `patch()`를 사용하여 `SomeClass`를 모킹하고 싶다. 문제는 모듈 b를 임포트할 때 모듈 a에서 `SomeClass`를 import 해야 한다는 것이다. `patch()`를 사용하여 `a.SomeClass`를 모킹하면 우리가 의도한대로 테스트에 영향을 미칠 수 없다.
  따라서 핵심은 `SomeClass`가 사용되는 곳(조회되는 곳)을 패치하는 것이다. 이 경우 `some_function`은 우리가 import 한 모듈 b에서 `SomeClass`를 조회한다. 따라서 패치는 다음과 같아야 한다.

  ```python
  @patch('b.SomeClass')
  ```

  하지만, `from a import SomeClass` 대신 모듈 b가 `import a`를 수행하고 `some_function`이 `a.SomeClass`를 사용하는 시나리오에서는 패치하려는 클래스가 모듈에서 직접 조회되므로(`a.SomeClass.bulabula`) 대신 `a.SomeClass`를 패치해야 한다.

  ```python
  @patch('a.SomeClass')
  ````

mock은 세 가지 decorator를 제공한다. `patch(), patch.object(), patch.dict()`. `patch`는 패치 할 attribute를 지정하기 위해 `package.module.Class.attribute` 형식의 단일 문자열을 취한다.
