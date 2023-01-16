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

## 04. 서술적인 이름을 사용하라

* 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
* 길고 서술적인 이름이 길고 서술적인 주석보다 좋다.
* 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.

(하지만, 모든 정보를 이름에 담으려고 하지는 말자. 정보가 가득한 이름보다는, 읽기 쉬운 이름을 만드는 것임에 집중하자.)

## 05. 함수 인수

* 함수에서 이상적인 인수 개수는 0개(무항)다.
  * 다음은 1개고, 다음은 2개다.
  * 3개 이상은 가능한 피하는 편이 좋다.
  * 4개 이상은 특별한 이유가 필요하다. 특별한 이유가 있어도, 사용하면 안 된다.
* 출력 인수는 입력 인수보다 이해하기 어렵다.
  * return 받을 값을 인수로서 넣지 말자.
  * 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.

추가적으로, DI는 인수라고 보기 어렵다. 생성자를 만들 때, 외부에서 쓰일 모듈을 넣어주는 것이다.  
테스트하기가 쉬워지기 때문이다(mock 인스턴스를 넣어주면 됨)

### 많이 쓰는 단항 형식

함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지이다.

* 인수에 질문을 던지는 경우
  * boolean fileExists("MyFile")
* 인수를 뭔가로 변환해 결과를 반환하는 경우
  * InputStream fileOpen("MyFile")
* 드물지만, 이벤트 역시 단항 함수 형식이다.
  * 이벤트는 입력 인수만 있고, 출력 인수는 없다.
  * 이벤트 함수는 조심해서 사용한다. 이벤트라는 사실이 코드에 명확히 드러나야 한다.

위 언급된 경우를 제외하면 단항 함수는 가급적 피한다.

### 플래그 인수

플래그 인수는 추하다. 함수로 부울 값을 넘기는 관례는 정말로 끔찍하다.

### 이항 함수

좌표같이 자연스럽게 이항 함수 형태를 취해야 하는 것들도 있다.  
하지만 대부분의 경우, 이항함수는 되도록 다항 함수로 변환하도록 한다. 예를 들어,

```java
writeField(outputStream, name)
```

보다는

```java
outputStream.writeField(name)
```

처럼 writeField를 outputStream의 method로 만드는 편이 좋다.

### 인수 객체

인수가 2~3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어 본다.

### 인수 목록

가변 인수는 단항, 이항, 삼항 함수로 취급할 수 있다.

```python
someFunction(**kwaargs)
```

### 동사와 키워드

함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다.  
단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다. 예를 들면

```python
write(name)
```

또한, 함수 이름에 키워드(인수 자체)를 추가하는 것도 고려해보라. 예를 들면

```python
assertExpectedEqualsActual(expedted, actual)
```

## 06. 부수 효과를 일으키지 마라

부수 효과를 일으키면 한 함수에서 두 가지 이상의 일을 하게 되는 것이다.  
아래 코드에서는 부수 효과를 일으킨다.

```java
public class UserValidator {
    private Cryptographer cryptographer;

    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```

위 함수에서는 password를 확인할 뿐 아니라, valid할 경우 session을 initialize하고 있다. 그리고, 해당 부수 효과는 함수 이름에서 그 실행을 짐작할 수조차 없다.

## 07. 명렁과 조회를 분리하라

뭔가를 조회하고, 설정하는 것을 한 함수 내에서 하지 말라.

## 08. 오류 코드보다 예외를 사용하라

오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다.  
오류 코드를 반환할 바에는 예외를 사용하라

## 09. Try/Catch 블록 뽑아내기

Try/Catch 블록은 별도 함수로 뽑아내는 편이 좋다.  
오류 처리도 한 가지 작업이므로, 하나의 함수가 하나의 역할만 한다는 전제에 입각하여 오류 처리하는 함수는 오류만 처리해야 한다.

```java
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    }
    catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

위 예제처럼 delete는 모든 예외 처리까지 다 해주는 함수이고, 실제 동작은 deletePageAndAllReference 함수에서 수행한다.

## 10. 반복하지 마라

반복하지 말아야 하는 것은 개발자라면 누구나 다 알 것이다. 그걸 강조해준다!
