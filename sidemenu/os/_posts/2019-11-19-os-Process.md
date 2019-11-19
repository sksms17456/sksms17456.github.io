---
layout: post
title: 프로세스란?
description: >
    [프로세스의 개념을 알아보자!]
excerpt_separator: <!--more-->

---

<!--more-->

# 프로세스(Process)

#### "Process is a program in execution"



### 프로세스의 문맥(context)

- CPU 수행 상태를 나타내는 하드웨어 문맥
  - `Program Counter` 가 어디를 가르키는지!
  - 각종 `register` 에 어떤 값을 가지고 있는지!
- 프로세스의 주소 공간
  - `code`, `data`, `stack` 에 어떤 내용이 들어가 있는지!
- 프로세스 관련 커널 자료 구조
  - `PCB(Process Control Block)`
  - `Kernel stack`



#### Kernel stack

- 각 프로세스가 자기 자신의 코드를 실행중일 때 함수호출이 이루어지면 자신의 `stack`에 함수를 호출한 후 리턴하고 관련 정보를 쌓음
- 프로세스를 실행 중 본인이 할 수 없는 일을 운영체제에 요청(`system call`)을 하면 PC가 `kernel`의 코드를 실행시켜 **kernel stack**에 정보를 쌓음



### 프로세스의 상태(Process State)

- **이 상태들은 CPU관점에서의 상태들을 나타낸다.**

- **Running**
  - CPU를 잡고 `instruction`을 수행중인 상태
- **Ready**
  - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족한 상태)
  - 보통 Ready상태의 프로세스들이 CPU를 번갈아가면서 잡는다.
- **Blocked(wait, sleep)**
  - CPU를 주어도 당장 `instruction`을 수행할 수 없는 상태
  - 프로세스 자신이 요청한 이벤트가 즉시 만족되지 않아 이를 기다리는 상태
  - 프로그램 본인이 CPU를 잡고 일을 하다가 I/O작업 같은 다른 오래걸리는 작업을 위해 다른곳으로 가서 CPU를 떠나서 일을 하고 있는 상태
  - 자신이 요청한 event가 만족되면 Ready상태로 돌아간다.
- **Suspended(stopped)**
  - 외부적인 이유로 프로세스의 수행이 정지된 상태(`Swapper`나 사용자에 의해서(`break key`))
  - 메모리를 통째로 빼앗아버리기 때문에 일을 아예 하지않고 멈춰있는 상태
  - 프로세스는 통째로 디스크에 `swap out`된다.
  - 외부에서 resume을 해 주어야 다시 Active한 상태가 된다.
- **New**
  - 프로세스가 생성중인 상태
- **Terminated**
  - 수행(execution)이 끝난 상태
  - 보통 `instruction`이 끝나면 정리가 조금 필요하여 정리중인 상태



### Process Control Block(PCB)

- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
- OS가 관리상 사용하는 정보
  - Process state, Process ID
  - scheduling information, priority
- CPU 수행 관련 하드웨어 값
  - Program counter, registers
- 메모리 관련
  - Code, data, stack의 위치 정보
- 파일 관련
  - Open file descriptors



### 문맥 교환(Context Switch)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행

  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

- **System** **call**이나 **Interrupt** 발생시 반드시 `context switch`가 일어나는 것은 아님

  - **프로세스 A**가 `timer interrupt`를 발생시킴

    -> `timer intterupt`는 CPU를 다른 프로세스에게 넘기기 위한 의도를 가졌기 때문에 운영체제 커널은 **프로세스 A**가 할당된 시간이 끝남을 알고 **프로세스 B**에게 CPU를 넘김

  - **프로세스 A**가 **I/O** 같은 시간이 오래 걸리는 작업을 요청(System call)함

    -> I/O요청을 끝낸 후 **프로세스 A**에게 다시 CPU를 줘 봤자 당장 `instruction`실행이 불가하기 때문에 당장 실행 가능한 Ready상태의 **프로세스 B**에게 CPU를 넘김

- 문맥교환이 일어나 CPU가 프로세스 A에서 프로세스 B로 넘어가기 위해서는 그 프로세스가 사용하던 `cache memory`를 전부 지워야 하기 때문에 부담이 크다.



### 프로세스를 스케쥴링하기 위한 큐

- **프로세스들은 각 큐들을 오가며 수행된다.**

- **Job** **queue**
  - 현재 시스템 내에 있는 모든 프로세스의 집합
- **Ready** **queue**
  - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- **Device queues**
  - I/O device의 처리를 기다리는 프로세스의 집합



### 스케쥴러(Scheduler)

- **장기 스케쥴러(Long-term scheduler or job scheduler)**
  - 프로세스가 시작(New) 되어 Ready상태로 넘어오는 과정에서 **admitted**(메모리에 올라오는 것을 허락)을 결정
  - **degree of Multiprogramming**을 제어(메모리에 프로그램이 얼마나 올라갈 수 있는지를 결정)
  - 보통 우리가 사용하는 `time sharing system`에는 장기 스케쥴러가 없음(프로그램이 시작이 되면 곧바로 메모리에 올려버림), 이를 중기 스케쥴러에서 제어한다.
- **단기 스케쥴러(Short-term scheduler or CPU scheduler)**
  - 굉장히 짧은 시간단위로 스케쥴링이 이루어짐(millisecond 단위)
  - 다음번에 어떤 프로세스에게 CPU를 줄지(running시킬지) 결정
- **중기 스케쥴러(Medium-term scheduler or Swapper)**
  - 장기 스케쥴러에서 프로그램들을 곧바로 메모리에 모두 올려버리면 메모리에 너무 많은 프로그램이 동시에 존재하게 된다.
  - 여유 공간 마련을 위해 일부 프로세스를 골라 통째로 메모리에서 디스크로 쫓아낸다.