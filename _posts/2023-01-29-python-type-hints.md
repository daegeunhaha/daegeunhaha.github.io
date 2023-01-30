---
layout: post
title: Python type hits
subtitle: Why, when we need to use type hints
tags: [study, general, python]
comments: true
---

python type hint에 대해 자세히 적어놓은 article을 하나 찾았기에, 번역을 하며 몇 가지 정리해보려고 한다.  
python type hint는 유용하지만, 일종의 주석 같은 역할이다. 코드의 실행에는 실제 영향을 미치지 않기에 어찌보면 주석처럼 계속 관리를 해주어야 하지만,  
주석과는 다르게 mypy 같은 tool을 활용해 코드의 static type checking을 할 수 있고, 가독성에도 도움이 된다.  
하지만, function type hint와 variable type hint를 모두 쓰자니 코드 작성이 너무 번거롭게 느껴질 것 같고, 이럴 바에는 자바를 쓰는 게 낫지 않나 하는 생각이 든다.  

article을 다 읽은 후 내 생각을 다시 정리해보겠다.

[12 Beginner Concepts About Type Hints To Improve Your Python Code](https://towardsdatascience.com/12-beginner-concepts-about-type-hints-to-improve-your-python-code-90f1ba0ac49)

위 Article에서 variable type hint 활용에 대한 답은 얻지 못했고, TypedDict가 유용하다는 건 알게 되었다.  
(TypedDict는 Dict 안에 Key, Value type이 mix 되어 있을 때 활용 가능하다.)

더 알아보기는 어려우니, 우선 생각을 정리해두어야겠다. 일반 variable까지 일단 typing을 적용해보자.  
그러고 나서 느낀 점을 posting해보는 것도 좋을 것 같다.  
당장은 tdd 예제에서부터 시작해보려 한다.

아래는 python typing 관련한 official document이다.
[typing — 형 힌트 지원](https://docs.python.org/ko/3/library/typing.html#module-typing)

아래는 Type hint를 조금 더 깊게 쓸 수 있는 방법들이다. 참고하길 추천한다. (기회가 되면 따로 포스팅을 통해 정리해두는 것도 좋겠다.)

[파이썬 Typing 파헤치기 - 기초편](https://sjquant.tistory.com/68)
[파이썬 Typing 파헤치기 - 심화편](https://sjquant.tistory.com/69)
