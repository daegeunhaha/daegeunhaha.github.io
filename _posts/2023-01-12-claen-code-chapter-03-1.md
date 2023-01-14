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

## 03. Switch문

Switch - Case문은 필연적으로 문제를 발생시킨다.

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
    switch (e.type) {
        case COMISSIONED:
            return calculateCommissionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
        default:
            throw new InvalidEmployeeType(e.type);
    }
}
```

위 예시를 보자. 다음의 문제들을 가지고 있다.

* 함수가 길다. 새 직원 유형을 추가하면 더 길어진다. (OCP를 위반한다.)
* 한 가지 작업만 수행하지 않는다. (SRP를 위반한다.)
* 위 함수와 구조가 같은 함수가 무한정 존재한다. (직원 유형에 따라 다르게 수행하는 것들)  
  * isPayday(Employee e, Date date);  
  * deliverPay(Employee e, Money pay);

이럴 때는 추상 팩토리 패턴 (Abstract Factory pattern)에 숨겨 Employee 객체를 상속하는 여러 Employee들을 만들고, 해당 객체들에서 이러한 함수들에 대한 구현을 직접 하도록 한다.  
Switch case는 Factory class에서 단 한 번만 사용한다. Employee의 type을 받아 서로 다른 Employee를 생성할 수 있게 한다.

```java
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}
```

```java
public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
```

```java
public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch(r.type) {
            case COMISSIONED:
                return new CommissionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmployee(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```

**이렇게, Switch case는 다형적 객체를 생성하는 코드 안에서 단 한 번만 허용하는 것으로 한다.**
