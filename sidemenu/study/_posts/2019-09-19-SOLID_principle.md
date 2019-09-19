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
- 만약 하나의 클래스가 하나 이상의 책임을 가지고 있다면 이는 결합(Coupled)을 발생시키고, 하나의 책임에 대한 변경은 다른 책임의 수정을 발생시키게 된다.
- 이 원칙의 적용은 클래스에만 국한되지 않으며, 소프트웨어 컴포넌트와 마이크로 서비스에도 적용된다!!

```java
class Student {
    String name;
    public Student(String name){
        this.name = name;
    }
    getName(){}
    saveStudent(Student student){}
}
```
위 예제 코드는 하나의 클래스에 다음 두 가지 책임을 가지고 있다.
1. Student DB 관리의 책임
2. Student property 관리의 책임
- `saveStudent()`가 DB의 Student를 관리하는 동안 생성자와 `getName()`은 Student의 property를 관리하게 된다.

