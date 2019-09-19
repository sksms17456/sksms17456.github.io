---
layout: post
title: SOLID 원칙
description: >
    SOLID원칙을 통해 읽기 쉽고 유지보수가 쉬운 프로그램을 만들자!

excerpt_separator: <!--more-->
---

<!--more-->

## SOLID 원칙

### S : Single Responsibility Principle(단일 책임의 원칙)
- 단일 클래스는 `오직 하나의 책임`을 가져야 한다.
- 만약 하나의 클래스가 하나 이상의 책임을 가지고 있다면 결합(Coupled)발생. 이 경우 하나의 책임에 대한 변경은 다른 책임의 수정을 발생시키게 된다.
- 이 원칙의 적용은 클래스에만 국한되지 않으며, 소프트웨어 컴포넌트와 마이크로 서비스에도 적용된다!!

#### 적용방법
`Divergent change`
- Extract Class를 통해 혼재된 각 책임을 각각의 개별 클래스로 분할하여 클래스 당 하나의 책임만을 맡도록 하는 것
- Extract Class들의 유사한 책임들을 부모에게 명백히 위임하고 다른 책임들은 각자에게 정의

`Shotgun surgery`
- 산발적으로 분포된 책임들을 한 곳에 모음으로써 응집도를 높이는 작업
- Move Field와 Move Method를 통해 책임을 기존의 어떤 클래스로 모으고나, 이럴만한 클래스가 없다면 새로운 클래스를 만들어 해결


> 클래스들이 같은 이유로 매번 변화하는 변화경향이 있다면, 클래스를 설계할 때 연관된 기능들을 함께 모으는 것을 목표로 해야한다. 우리는 기능을 분리하도록 노력하고, 기능들은 서로 다른 이유로 변경되어야 한다. - Steve Fenton

------

### O : Open-Closed Principle(개방-폐쇄 원칙)

### L : Liskov Substitusion Principle(리스코프 치환 법칙)

### I : Interface Segregation Principle(인터페이스 분리 원칙)

### D : Dependency Inversion Principle(의존성 역전 법칙)