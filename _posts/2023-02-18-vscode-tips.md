---
layout: post
title: VScode tips
subtitle: Useful vscode tips
tags: [study, general, vscode]
comments: true
---

vscode 꿀팁들에 대해 정리하는 post이다.

### settings.json

vscode를 사용하다보면 preference를 조작할 일이 많이 생긴다.  
console로 조작해 놓은 경우에는 설정을 복사하기 어려운데, settings.json 파일을 사용하면 이런 고민을 해결할 수 있다.

다만, settings.json 파일을 건드려도 설정이 적용되지 않는 경우도 있었다.

이 때는, preference setting이 어떤 scope로 존재하는지 확인해봐야 한다.

user, remote-ssh, workspace 별로 setting이 존재하며, 오른쪽의 것들이 왼쪽의 것을 덮어쓰는 형태인 것 같다.

### pylint, black 적용

나는 앞으로 pylint(error checking, code style checking) + mypy(type checking)을 사용하려고 한다.  
pyright이 더 좋다고 함에도 mypy를 사용하려고 하는 이유는, pyright의 사용법이 어렵게 느껴지기 때문이다.  
무슨 설정 파일이 따로 필요한 것 같던데..

pylint, mypy 및 black 등의 설정을 setup.cfg 에서 할 수 있었던 것 같다.

이 외에도, pylintrc 등의 사용법을 알아야 하고

vscode에도 자동으로 이같은 것들이 적용될 수 있도록 설정하는 법을 알아야 한다.

위의 3개를 공부해서 posting하도록 하겠다.
