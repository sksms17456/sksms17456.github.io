---
layout: post
title: CPU Scheduling2
description: >
    [CPU 스케쥴링에 계속 알아보자!]
excerpt_separator: <!--more-->

---

<!--more-->

### Multilevel Queue

> ↑↑ **Highest priority**
>
> system processes
>
> interactive processes
>
> interactive editing processes
>
> batch processes
>
> student processes
>
> ↓↓ **Lowest priority**

- `Ready queue`를 여러 개로 분할
  - **foreground**(interactive)
  - **background**(batch - no human interaction)
- 각 queue는 특성에 맞는 독립적인 스케쥴링 알고리즘을 가진다.
  - **foreground - RR**
  - **background - FCFS**
- 어떤 queue에게 CPU를 줄지에 대한 스케쥴링이 필요
  - **Fixed priority scheduling**
    - `foreground`가 비어있을 때에만 `background`에 CPU를 준다.
    - `background`에서 `starvation`이 발생할 수 있다.
  - **Time slice**
    - 각 큐에 CPU time을 적절한 비율로 할당
    - ex) 80%는 `foreground`에게, 20%는 `background`에게 할당한다.

- 우선순위가 높은 프로세스들이 대기하고 있으면 무조건 우선순위가 높은 프로세스들부터 CPU를 할당해준다.





### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동 가능
- 처음 들어오는 프로세스는 우선순위가 가장 높은 큐에 집어넣는다.
- 우선순위가 가장 높은 큐는 RR에서 `time-quantum`을 가장 짧게 준다.
- 할당된 `time-quantum`에 일을 끝내지 못하면 다음 우선순의 큐에 들어가게 된다.
- 우선순위가 낮은 큐로 갈수록 RR의 `time-quantum`을 점점 길게 주다가 가장 우선순위가 낮은 큐는 FCFS스케쥴링을 적용한다.



### Example of Multilevel Feedback Queue

- **Three queues**
  - Q<sub>0</sub> : time-quantum *8ms*
  - Q<sub>1</sub> : time-quantum *16ms*
  - Q<sub>2</sub> : FCFS
- **Scheduling**
  - new job이 **Q<sub>0</sub>**로 들어간다.
  - CPU를 잡아서 **Q<sub>0</sub>**의 할당 시간(*8ms*) 동안 수행된다.
  - *8ms* 동안 다 끝내지 못했으면 **Q<sub>1</sub>**으로 내려간다.
  - **Q<sub>1</sub>**에 줄서서 기다렸다가 CPU를 잡아서 **Q<sub>1</sub>**의 할당 시간(*16ms*) 동안 수행된다.
  - 16 ms에 끝내지 못한 경우 **Q<sub>2</sub>**로 내려간다.



### Multiple-Processor Scheduling

- CPU가 여러 개인 경우에는 스케쥴링이 더욱 복잡해진다.
- **Homogeneous processor**
  - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
  - 반드시 **특정 프로세서**에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해진다.
- **Load sharing**
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘
  - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- **Symmetric Multiprocessing(SMP)**
  - 모든 CPU들이 대등하기 때문에 각 프로세서가 각자 알아서 스케쥴링을 결정한다.
- **Asymmetric Multiprocessing**
  - 하나의 프로세서가 전체적인 컨트롤(시스템 데이터의 접근과 공유)을 담당하고 나머지 프로세서는 거기에 따른다.



### Real-time Scheduling

- **Hard real-time systems**
  - 정해진 시간 안에 반드시 끝내도록 스케쥴링해야한다.
- **Soft real-time systems computing**
  - 다른 프로세스에 비해 우선순위만 조금 높여준다.



### Thread Scheduling

- **Local Scheduling**
  - `User level thread`의 경우 사용자 수준의 `thread library`에 의해 어떤 `thread`를 스케쥴할지 결정
  - 운영체제는 `thread`의 존재를 모르기 때문에 운영체제 입장에서는 그냥 그 프로세스에게 CPU를 줄지 안줄지만을 결정하고, 어떤 `thread`에게 CPU를 줄지는 프로세스 내부에서 직접 결정한다.
- **Global Scheduling**
  - 운영체제가 `thread`의 존재를 알고 있기 때문에 프로세스를 스케쥴링 할 때 처럼 커널의 단기 스케쥴러가 어떤 `thread`에게 CPU를 줄지 결정한다.



### Algorithm Evaluation

- **Queueing models**
  - 확률 분포로 주어지는 `arrival rate`(프로세스가 `server`에 도착하는 도착률)와 `service rate`(단위 시간당 처리할 수 있는 처리율) 등을 통해 각종 `performance index` 값을 계산한다.
  - 예전에는 많이 쓰이던 방식이지만, 최근에는 실제 시스템에서 직접 돌려보는 방식을 더 의미있게 보기 때문에 많이 사용하지는 않는다.
- **Implementation(구현) & Measurement(성능측정)**
  - 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 및 비교한다.
- **Simulation(모의 실험)**
  - 알고리즘을 모의 프로그램으로 작성후 **trace**를 입력으로 하여 결과를 비교한다.
  - **trace**
    - 임의로 만들 수도 있고, 실제 프로그램을 돌리면서 뽑을 수도 있다.
    - Simulation에 들어가는 input data