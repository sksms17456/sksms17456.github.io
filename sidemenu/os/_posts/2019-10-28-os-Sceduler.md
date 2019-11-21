---
layout: post
title: Sceduler
description: >
    [운영체제 - 스케쥴러]
excerpt_separator: <!--more-->

---

<!--more-->

## 스케줄러

### 장기스케줄러(Long-term scheduler or job scheduler)
- 메모리와 디스크 사이의 스케줄링을 담당한다.
- 한정된 메모리에 많은 프로세스들이 한꺼번에 올라올 경우, 대용량 메모리(pool)에 임시적으로 저장된다.
- 이 pool에 저장되어 있는 프로세스 중 어떤 프로세스에 메모리를 할당하여 ready queue로 보낼지 결정하는 역할을 한다.
- 프로세스의 상태 : new -> ready(in memory)

### 단기스케줄러(Short-term scheduler or CPU scheduler)
- CPU와 메모리 사이의 스케줄링을 담당한다.
- ready queue에 존재하는 프로세스 중 어떤 프로세스를 running 시킬지 결정
- 프로세스에 CPU를 할당(scheduler dispatch)
- 프로세스의 상태 : ready -> running -> waiting -> ready

### 중기스케줄러(Medium-term scheduler or Swapper)
- 현 시스템에서 메모리에 너무 많은 프로그램이 동시에 올라가는 것을 조절하는 스케줄러
- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫒아낸다.(swapping)
- 프로세스에게서 메모리를 deallocate한다.
- 프로세스의 상태 : ready -> suspended
- suspended
  - 외부적인 이유로 프로세스의 수행이 정지된 상태로 메모리에서 내려간 상태
  - 프로세스 전부 디스크로 swap out된다.
  - blocked 상태는 다른 I/O작업을 기다리는 상태이기 때문에 스스로 ready state로 돌아갈 수 있지만 이 상태는 외부적인 이유로 suspending 되었기 때문에 스스로 돌아갈 수 없다.

## 프로세스의 상태
- new : 프로그램을 실행하기 위해 OS에게 요청하여 프로세르를 생성한 상태
- ready : 실행 가능한 상태 or running중 인터럽트에 의해 다시 ready가 된 상태
- running : 스케쥴러에 의해 실행되고 있는 상태
- waiting : I/O 혹은 이벤트 대기에 의해 대기중인 상태, waiting 상태였던 프로세스는 I/O나 이벤트가 완료됨에 따라 다시 ready상태가 된다.
- terminated : 실행 중이던 프로그램이 종료됨. 곧 OS에 의해 자원이 회수된다.


## CPU 스케줄러

### FCFS(First Come First Served)
- 비선점형
- 먼저 온 순서대로 처리
- 일단 CPU를 잡으면 CPU burst가 완료될 때까지 CPU를 반환하지 않는다. 할당되었던 CPU가 반환될 때에만 스케줄링이 이루어진다.
#### 문제점
- convoy effect
  - 소요시간이 긴 프로세스가 먼저 도달하여 효율성을 낮추는 현상이 발생

### SJF(Shortest-Job-First)
- 비선점형
- 다른 프로세스가 먼저 도착했어도 CPU burst time이 짧은 프로세스에게 선 할당
#### 문제점
- starvation
  - 극단적으로 CPU사용이 짧은 job을 선호하기 때문에 사용시간이 긴 프로세스는 거의 영원히 CPU를 할당받을 수 없는 차별을 받게된다.

### SRT(Shortest Remaining time First)
- 선점형
- 새로운 프로세스가 도착할 때마다 새로운 스케줄링이 이루어진다.
- 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 뺏긴다.
#### 문제점
- starvation
- 새로운 프로세스가 도달할 때마다 스케줄링을 다시하기 때문에 CPU burst time을 측정할 수가 없다.

### Priority Scheduling
- 우선순위가 가장 높은 프로세스에게 CPU를 할당한다. 여기서 우선순위란 정수로 표현하게 되고 작은 숫자가 우선순위가 높다.
- 선점형
  - 더 높은 우선순위의 프로세스가 도착하면 실행중인 프로세스를 멈추고 CPU를 선점한다.
- 비선점형
  - 더 높은 우선순위의 프로세스가 도착하면 ready queue의 head에 넣는다.
#### 문제점
- starvation
- 무기한 봉쇄(Indefinite blocking)
  - 실행 준비는 되어있으나 CPU를 사용못하는 프로세스를 CPU가 무기한 대기하는 상태
#### 해결책
- aging
  - 아무리 우선순위가 낮은 프로세스라도 오래 기다리면 우선순위를 높여주자.

### Round Robin
- 각 프로세스는 동일한 시간의 할당 시간을 받고, 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.
- CPU 사용시간이 랜덤한 프로세스들이 섞여있을 경우에 효율적
- 프로세스의 context를 save할 수 있기 때문에 이러한 스케줄링이 가능하다.
- n개의 프로세스가 ready queue에 있고 할당시간이 q인 경우 각 프로세스는 q 단위로 CPU시간의 1/n을 얻기 때문에 어떤 프로세스도 (n-1)q 시간 이상 기다리지 않는다.
- 프로세스가 기다리는 시간이 CPU를 사용할 만큼 증가하기 때문에 공정한 스케줄링이라고 할 수 있다.
#### 주의
- 설정한 할당시간이 너무 커지면 FCFS와 같아진다.
- 설정한 할당시간이 너무 작아지면 잦은 context switch로 overhead가 발생하기 때문에 적당한 할당시간을 선점하는 것이 중요하다.

