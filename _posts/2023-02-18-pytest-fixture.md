---
layout: post
title: Pytest Fixtures
subtitle: pytest의 fixture는 어떻게 활용하는 걸까
tags: [study, general, python]
comments: true
---

### Fixture

Fixture란, 테스팅 수행 전/후로 필요한 부분들을 가지고 있는 코드, 리소스를 의미한다.

### Pytest Fixture

Pytest에서의 Fixture은 다음과 같은 특징들을 가지고 있다.

1. python decorator를 사용한다.
    * @pytest.fixture decorator 사용
2. method 처럼 정의하고, method의 이름을 testcase function에서 인자로 받음으로써 fixture의 return 값을 얻을 수 있다.
3. 4가지의 scope이 있다.
    1. Session
        * the fixture is destroyed at the end of the test session.
    2. Package
        * the fixture is destroyed during teardown of the last test in the package. (folder)
    3. Module
        * the fixture is destroyed during teardown of the last test in the module. (file)
    4. Class
        * the fixture is destroyed during teardown of the last test in the class.
    5. Function
        * the default scope, the fixture is destroyed at the end of the test.
4. scpoe은 decorator와 함께 정의해준다.
    * @pytest.fixture(scope='function')
5. 재사용이 가능하다.
    * 한 번 선언된 fixture는 재사용이 가능하다.
    * fixture 간에도 재사용이 가능하다.
6. 한 번에 여러 fixture를 호출할 수 있다.
7. yield를 사용하여, yield 이후에 tearDown 설정들을 해놓을 수 있다.
8. conftest.py에 fixture를 선언하면, 다른 test code들에서도 해당 fixture를 공유할 수 있다.

### References

[파이썬 테스트(unit test) 코드 작성 라이브러리 pytest 와 fixture](https://toramko.tistory.com/entry/python-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%ED%85%8C%EC%8A%A4%ED%8A%B8unit-test-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-pytest-%EC%99%80-fixture)
[[TDD] Python Testing Framework 1 - Pytest, unittest](https://libertegrace.tistory.com/entry/TDD-Python-Testing-Framework-Pytest-unittest)
[[pytest] python 코드를 테스트 해봅시다.](https://binux.tistory.com/47)
