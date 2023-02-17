---
layout: post
title: pytest vs unittesst
subtitle: python unittest framework으로 어떤 게 더 나을까
tags: [study, general, python]
comments: true
---

### unit test 작성

회사에서 unit test를 작성해야 하는데, 작성된 코드는 unittest라는 library를 사용 중이었으나,  
정작 pytest 라는 명령어로 테스트를 run하고 있던 것이 이상하였다.

찾아보니, 둘은 다른 framework이며, 보통 pytest를 더 선호한다고 한다.  
둘이 어떤 차이점이 있는지, 왜 pytest를 쓰는 것이 더 나은지 여기에 정리해보려고 한다.

참고 article: [pytest vs unittest](https://www.bangseongbeom.com/unittest-vs-pytest.html)
공식 document: [official_document](https://docs.pytest.org/en/latest/contents.html)

### unittest의 단점

* 클래스를 정의해야 한다.
  * 테스트 하나에 클래스 하나를 정의해야 한다.
* camel case 를 사용한다. (PEP8 에서는 snake case를 함수명으로 사용하기를 더 권장한다.)
  * assertEquals() 같은 함수

### pytest의 장점

* fixture 문법
  * unittest가 xUnit style의 setUp / tearDown을 사용하는 반면, pytest는 독특한 fixture 문법을 사용한다.
  * 자세한 내용은 다른 post에 정리하도록 한다.
* assert문의 편리함
  * unittest가 assertTrue, assertEquals 같은 method를 상황에 따라 다르게 사용해야 하는 반면,
  * pytest는 assert 하나로 이 모든 것을 해결한다.
* 다양한 고급 기능
  * 매개변수화된 fixture
  * 병렬 테스트 (하나의 테스트가 끝나지 않아도 다른 테스트 수행) -> 전체 수행 시간 단축
