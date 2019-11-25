---
layout: post
title: Process Synchronization
description: >
    [ Process Synchronization에 대해 알아보자!]
excerpt_separator: <!--more-->

---

<!--more-->
# Process Synchronization

### Process Synchronization 문제

- 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process)간의 실행 순서(orderly execution)를 정해주는 메커니즘이 필요하다.



### Race Condition

- 한정된 자원을 동시에 이용하려는 여러 프로세스가 자원의 이용을 위해 경쟁을 벌이는 현상
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- 이를 막기 위해서는 `concurrent process`는 동기화(synchronize)되어야 한다.



### Race Condition은 언제 발생하는가?

![racecondition1](/assets/img/post/os/racecondition1.png)
- 커널모드 running 중 interrupt가 발생하여 인터럽트 처리루틴이 수행
  - 양쪽 다 커널 코드이므로 `kernel address space`를 공유한다.
  - 커널 작업이 끝날 때까지는 인터럽트를 disable시켰다가 작업이 끝난 후에 enable시키는 방법으로 해결한다.

![racecondition2](/assets/img/post/os/racecondition2.png)
- Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
  - 프로세스가 커널 모드에 있을때는 할당시간이 끝나도 CPU를 `preempt`하지 않고 커널 모드에서 사용자 모드로 돌아갈 때 CPU를 `preempt`하여 해결한다..

![racecondition3](/assets/img/post/os/racecondition3.png)
- **Multiprocessor**에서 shared memory 내의 kernel data
  - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock을 하는 방법으로 해결한다.
  - 한 번에 하나의 CPU만이 커널에 들어갈 수 있게 하여 해결한다.



### Critical-Section(임계 영역)

- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우 각 프로세스의 `code segment`에는 공유 데이터를 접근하는 코드인 **critical section**이 존재한다.
- 하나의 프로세스가 **critical section**에 있을 때 다른 모든 프로세스는 **critical section**에 들어갈 수 없어야 한다.

