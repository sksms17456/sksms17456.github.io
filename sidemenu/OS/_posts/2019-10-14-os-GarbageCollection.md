---
layout: post
title: Java Garbage Collection
description: >
excerpt_separator: <!--more-->

---

# Java Garbage Collection

## Garbage Collector
- Java에서 명시적으로 메모리를 해제하기 위해 `System.gc()`를 호출하는 경우 시스템의 성능에 매우 큰 영향을 끼치게 된다.
- 이러한 이유로 **Garbage Collector**가 더 이상 필요 없는 (쓰레기)객체를 찾아 지우는 작업을 한다.

## weak generational hypothesis
#### Garbage Collector의 두 가지 가정
- 대부분의 객체는 금방 `unreachable`상태가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.
- 이 두 가정의 장점을 최대한 살리기 위해 **Young, Old** 영역으로 물리적 공간을 나눔

## Garbage Collector와 Reachability(Root Set)
- Java GC는 객체가 Grabage인지 판별하기 위해 Reachability라는 개념을 사용한다.
- 어떤 객체에 유효한 참조가 있으면 reachable, 없으면 unreachable로 구별하여 unreachable객체를 garbage로 간주하여 GC를 수행한다.
- 한 객체는 여러 다른 객체를 참조하고, 참조된 달느 객체들도 마찬가지로 또 다른 객체들을 참조할 수 있으므로 객체들은 참조사슬을 이루게 된다.
- 이러한 상황에서 유효한 참조 여부를 파악하려면 항상 유효한 최초의 참조가 있어야 하는데 이를 객체 참조의 Root set이라고 한다.

> #### 런타임 데이터 영역
> 1. 스레드가 차지하는 영역
> 2. 객체를 생성 및 보관하는 하나의 큰 힙
> 3. 클래스 정보가 차지하는 메서드 영역
>
> #### 힙에 있는 객체들에 대한 참조
> 1. 힙 내의 다른 객체에 의한 참조
> 2. Java 스택, 즉 Java 메서드 실행 시에 사용하는 지역 변수와 파라미터들에 의한 참조
> 3. native 스택, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
> 4. 메서드 영역의 정적 변수에 의한 참조

- 이들 중 힙 내의 다른 객체에 의한 참조를 제외한 나머지 3개가 Root set 으로, reachability를 판가름하는 기준이 된다.

## Young Generation 영역
- 새롭게 생성한 객체의 대부분이 이곳에 위치
- 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 **Young**영역에 생성되었다가 사라진다.
- 이 영역에서 객체가 사라질 때 `Minor GC`가 발생한다고 말한다.
#### Minor GC
- **Young** 영역은 **Eden**영역과 두 개의 **Survivor**영역으로 총 3개의 영역으로 나뉜다.
  1. 새로 생성한 대부분의 객체는 `Eden` 영역에 위치한다.
  2. `Minor GC`가 발생하면 `Eden`과 `Survivor1` 에 `Alive`되어있는 객체를 `Survivor2`로 복사한다.
  3. 여전히 `Survivor1`에 남아있는 `Alive`되어있지 않은 객체들과 `Eden`영역을 **Clear**한다.
  4. 다음번 `Minor GC`가 발생하면 이번엔 `Survivor2`와 `Eden`영역에서 `Survivor1`영역으로 복사하고, `Survivor2`와 `Eden`을 **Clear**한다.
  5. `Survivor`영역 간의 이동은 이동할 때마다 **age**값이 증가한다.
  6. 이런 식으로 `Minor GC`를 수행하다가, `Survivor`영역에서 **age**값이 특정 값 이상이 된 객체들은 가 `Old`영역으로 옮기게 된다.**(Promotion)**
- 이러한 방식의 GC 알고리즘을 **Copy&Scavenge**라고 한다. 이 방법은 속도가 매우 빠르기 때문에, 자주 일어나는 `Minor GC`의 경우에 적합한 알고리즘이다.

## Old Generation 영역(Tenured 영역)
- **Young**영역에서 살아남은 객체가 여기로 복사된다.
- 대부분 **Young**영역보다 크게 할당하며, 크기가 큰 만큼 **Young**영역보다 GC는 적게 발생한다.
- 이 영역에서 객체가 사라질 때 `Major GC`가 발생한다고 말한다.
- 기본적으로 데이터가 가득 차면 GC를 실행한다.
#### Major GC
- **Mark&Compact 알고리즘**
  1. 전체 객체들의 `reference`를 쭉 따라가면서 `reference`가 연결되지 않는 객체를` Mark`한다.
  2. 이 작업이 끝나면 사용되지 않는 객체들은 모두` Mark`가 되고, 이 객체들을 삭제한다.
  3. 실제로는 `Compact`라고 해서 `Mark`된 객체로 생기는 부분을 `Unmark`된 객체로 메꾸는 방법이다.

#### Full GC
- 힙 메모리 전체 영역(Young, Old영역 모두)을 Clear하는 작업이다.
- `Full GC`는 매우 속도가 느리며, `Full GC`가 일어나는 도중에는 순간적으로 `Java Application`이 멈춰 버리기 때문에 `Full GC`일어나는 정도와 소요되는 시간은 `Application`의 성능과 안정성에 아주 큰 영향을 준다.

## stop-the-world?
- `GC`를 실행하기 위해 `JVM`이 애플리케이션 실행을 멈추는 것
- `stop-the-world`가 발생하면 `GC`를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈추고, `GC`작업을 완료한 이후에야 중단했던 작업을 다시 시작한다.
- 어떤 `GC`알고리즘을 사용하더라도 `stop-the-world`는 발생하기 때문에, `GC튜닝`이란 이 `stop-the-world`의 시간을 줄이는 것이다.

## Permanent Generation 영역
- `Method Area`라고도 한다.
- 객체나 억류(intern)된 문자열 정보를 저장, **Old**영역에서 살아남은 객체가 영원히 남아있는 곳은 아니다.
- 이 영역에서 GC가 발생할 수도 있는데, 이도 `Major GC`의 횟수에 포함된다.


