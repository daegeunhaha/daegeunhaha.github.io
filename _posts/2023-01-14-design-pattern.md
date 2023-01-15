---
layout: post
title: Design Pattern
subtitle: 내가 이해한 Design Pattern
tags: [study, general, design_pattern]
comments: true
---

AA를 공부하며 Design pattern을 빡세게 공부했지만, 지금은 다 까먹고 말았다.  
근데 요거는 내가 Code를 짤 때 꽤 유용하게 사용할 수 있으므로, 여기에 다시 기록해 놓으려고 한다.

### Factory Method Pattern & Abstract Factory Pattern

우선, 내가 정리한 두 Design Pattern의 Class diagram은 다음과 같다.
[Factory_Pattern_Class_Diagram](/assets/img/factory.jpg)

두 패턴의 Class Diagram을 보면, 이 Design Pattern을 사용함으로써 얻을 수 있는 이익은 크게 다음과 같은 것으로 보인다.

1. Product를 추상화함
    * 공통된 함수들을 가지며, 같은 방식으로 호출할 수 있으나 내부 구현을 다르게 할 수 있다.  
      다시 말해서, 새로운 type의 Product가 생기면 해당 type의 Product를 새로 정의하기만 해도 기존 모든 Client 코드에는 변화가 없다.
2. Client 코드 내에서 직접 생성자를 호출하는 것이 아니라, 생성자(Factory) 클래스 속 함수를 통해 호출함
    * Testing이 쉬움. Factory 역시 의존성 주입을 통해 외부에서 공급받을 수 있다면, 테스트 시 생성자 클래스를 mocking할 수도 있고, 여러모로 테스트에 유리해짐
    * Product가 추가되어도, Client 코드에는 변화가 필요하지 않다.
        * 만약 Factory class가 단일로 존재하고, create method가 switch-case문 등을 통해 이루어진다면 새로운 Product가 추가되었을 때 이 Factory 코드만 바뀌면 된다.
3. Factory를 추상화함
    * 이 부분에 대해서는 딱히 이점을 잘 모르겠으나, create method 말고 다른 method들을 공유하는 서로 다른 Factory들을 만들 수도.. 있겠다.

Factory를 사용하여 Product를 생성하는 방법의 단점은 무엇이 있을까..? client 코드에서 new로 바로 생성자를 호출하는 것은 항상 별로인 것일까?  
고민이 필요한 부분이다.
