---
layout: post
title: Clean Code Chapter 03
subtitle: 함수
tags: [study, general, clean_code]
comments: true
---

## 01. 작게 만들어라

* if문 / else문 / while문 등에 들어가는 블록은 한 줄이어야 한다.
* 중첩 구조가 생길만큼 함수가 커져서는 안 된다.
* 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안 된다. 모든 건 읽고 이해하기 쉽게 하기 위함이다.

## 02. 한 가지만 해라

> **함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.**

* 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다.  
  _(내 생각: 추상화 수준이 다르면 두 동작이 나란히 놓이는 게 아니라 block 안에 한 동작이 들어갈 것 같아진다)_
* 함수 내 섹션이 나눠지면, 한 함수 안에서 여러 작업을 한다는 증거다.
* **내려가기 규칙**  
  코드는 위에서 아래로 이야기처럼 읽혀야 좋다.  
  > TO A와 B를 하려면, A를 하고, C를 하고, B를 해야 한다.  
  > &nbsp;&nbsp;TO A를 하려면..  
  > &nbsp;&nbsp;TO C를 하려면..