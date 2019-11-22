---
layout: post
title: CPU Scheduling
description: >
    [CPU 스케쥴링에 대해 알아보자!]
excerpt_separator: <!--more-->

---

<!--more-->

# CPU Scheduling

### CPU Scheduler
- Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

### Dispatcher
- CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
- 이 과정을 context switch(문맥 교환)라고 한다.

### CPU 스케쥴링이 필요한 경우
- Running -> Blocked(예 : I/O 요청하는 시스템 콜)
- Running -> Ready(예 : 할당시간만료로 timer interrupt)
- Blocked -> Ready(예 : I/O 완료 후 인터럽트)
- Terminate
- 1, 4번의 스케쥴링은 **nonpreemptive**(비선점형) : 강제로 CPU를 빼앗지 않고 자진 반납
- 2, 3번의 스케쥴링은 **preemptive**(선점형) : 강제로 CPU를 빼앗는다.

<br>

## Scheduling Criteria(성능 척도)

### **System 입장에서의 성능척도**
- CPU하나를 가지고 최대한 일을 많이 시켜야 한다!
- **CPU utilization(이용률)**
  - 전체 시간중에서 CPU가 놀지 않고 일한 시간의 비율
  - CPU는 가능한 한 바쁘게 일을 시켜라!!
  - **중국집ver**. 주방장이 놀지않고 일한 시간의 비율
- **Throughput(처리량)**
  - 주어진 시간 동안에 몇 개의 작업을 완료 했는지
  - **중국집ver**. 단위 시간동안 손님을 얼마나 보냈는지

### **Program 입장에서의 성능척도**
- 내가 CPU를 빨리 얻어서 빨리 끝나면 좋다!
- **Turnaround time(소요시간, 반환시간)**
  - CPU를 쓰기 위해 들어와서 다 쓰고 나갈 때까지의 걸린 시간
  - **중국집ver**. 손님이 중국집에 들어와서 식사를 다 하고 나갈 때까지의 시간
- **Waiting time(대기 시간)**
  - CPU를 쓰기 위해서 Ready Queue에 줄을 서서 기다리는 시간
  - **중국집ver**. 손님이 주문한 메뉴들을 기다리는 시간
- **Response time(응답 시간)**
  - Ready Queue에 들어와서 처음으로 CPU를 얻기까지 걸리는 시간
  - **중국집ver**. 손님의 음식 중에서 가장 먼저 음식이 나올 때까지의 시간(단무지느낌)

> Waiting time 과 Response time의 차이
>
> 선점형 스케쥴링의 경우 CPU를 강제로 빼았기면 다시 Ready Queue에 줄을 서서 기다렸다가 다시 CPU를 받는 것을 반복할 수 있다. Ready Queue에서 기다리는 모----든 시간을 합친게 Waiting time이다. 반면 Response time 은 최초로 CPU를 받기 까지의 시간만을 의미한다!

<br>

## Scheduling Algorithms

### FCFS(First-Come First-Served)
- 비선점형
- 먼저 온 순서대로 처리한다.
- 일단 CPU를 잡으면 CPU burst가 완료될 때까지 CPU를 반환하지 않는다. 할당되었던 CPU가 반환될 때에만 스케줄링이 이루어진다.
- **convoy effect**
  - 소요시간이 긴 프로세스가 먼저 도달하여 효율성을 낮추는 현상이 발생

### SJF(Shortest-Job-First)
- 각 프로세스의 다음번 CPU burst time을 가지고 스케쥴링에 활용
- 다른 프로세스가 먼저 도착했어도 CPU burst time이 가장 짧은 프로세스에게 CPU를 할당한다.
- **Nonpreemptive**
  - 일단 CPU를 `burst time`이 가장 짧은 프로세스에게 할당하면 다음에 더 짧은 `burst time`을 가지는 프로세스가 도착하더라도 CPU를 할당받은 프로세스의 수행을 보장한다.
- **Preemptive**
  - CPU를 `burst time`이 가장 짧은 프로세스가 할당받더라도 다음에 더 짧은 `burst time`을 가지는 프로세스가 도착하면 CPU를 빼앗기게 된다.
  - 주어진 프로세스들에 대해 **minimum average waiting time**을 보장한다.(optimal)
  - 이 방법을 **Shortest-Remaining-Time-First(SRTF)**이라고도 부른다.
- **문제점**
  - **starvation**(기아 현상)
    - 극단적으로 CPU사용이 짧은 job을 선호하기 때문에 사용시간이 긴 프로세스는 거의 영원히 CPU를 할당받을 수 없는 차별을 받게된다.
  - 새로운 프로세스가 도달할 때마다 스케줄링을 다시하기 때문에 CPU burst time을 미리 측정할 수가 없다.(과거의 CPU burst time을 이용하여 예측은 가능하다.) -> **exponential averaging**(이 식을 알아야할 것인가?!?!?!)

### Priority Scheduling
- 우선순위가 가장 높은 프로세스에게 CPU를 할당한다. 여기서 우선순위란 정수로 표현하게 되고 작은 숫자가 우선순위가 높다.
- **SJF 스케쥴링**도 일종의 Priority Scheduling으로 볼 수 있다.
- **Preemptive**
  - 더 높은 우선순위의 프로세스가 도착하면 실행중인 프로세스를 멈추고 CPU를 선점한다.
- **Nonpreemptive**
  - 더 높은 우선순위의 프로세스가 도착하면 ready queue의 head에 넣는다.
- **문제점**
  - **starvation(기아 현상)**
  - **무기한 봉쇄(Indefinite blocking)**
    - 실행 준비는 되어있으나 CPU를 사용못하는 프로세스를 CPU가 무기한 대기하는 상태
- **해결책** - **aging**
  - 아무리 우선순위가 낮은 프로세스라도 오래 기다리면 우선순위를 높여주자.

### Round Robin
- 각 프로세스는 동일한 크기의 **할당 시간(time quantum)**을 받고, 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.
- CPU 사용시간이 랜덤한 프로세스들이 섞여있을 경우에 효율적
- 프로세스의 context를 save할 수 있기 때문에 이러한 스케줄링이 가능하다.
- n개의 프로세스가 ready queue에 있고 할당시간이 q인 경우 각 프로세스는 q 단위로 CPU시간의 1/n을 얻기 때문에 어떤 프로세스도 (n-1)q 시간 이상 기다리지 않는다.
- 프로세스가 기다리는 시간이 CPU를 사용할 만큼 증가하기 때문에 공정한 스케줄링이라고 할 수 있다.
- 일반적으로 SJF보다 `average turnaround time`이 길지만 `response time`은 더 짧다.

#### 주의
- 설정한 할당시간이 너무 커지면 `FCFS`와 같아진다.
- 설정한 할당시간이 너무 작아지면 잦은 context switch로 인한 overhead가 발생한다.
- 적당한 할당시간을 설정해주는 것이 중요하다!!!!!(약 10~100ms)

