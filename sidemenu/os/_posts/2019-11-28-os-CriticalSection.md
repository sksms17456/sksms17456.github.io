---
layout: post
title: Critical section
description: >
    [Critical section의 해결방법에 대해 알아보자!!!!!!!]
excerpt_separator: <!--more-->

---

<!--more-->
# Critical section

### Critical section 문제를 풀기위한 충족 조건 3가지

- **가정**
  - 모든 프로세스의 수행 속도는 0보다 크다.
  - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

- **Mutual Exclusion(상호배제)**
  - 어떤 프로세스가 `critical section`을 수행 중이면 다른 모든 프로세스는 `critical section`에 접근하지 않아야 한다.

- **Progress(진행)**
  - `critical section`에 아무도 들어가 있지 않은 상황에서 어떠한 프로세스가 `critical section`에 들어가고자 한다면 들어가도록 해주어야 한다.
  - 당연한 이야기 같지만 코드를 잘못짜는 경우에는 `critical section`에 두 개의 프로세스의 동시 접근을 막으려다가 둘 다 들어가 있지 않음에도 불구하고 둘 다 접근하지 못하는 경우가 생길 수 있다.

- **Bounded Waiting(유한 대기)**
  - 프로세스가 `critical section`에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 `critical section`에 들어가는 횟수에 한계가 있어야 한다.
  - 특정 프로세스의 입장에서 `critical section`을 못들어가고 지나치게 오래 기다리는 `starvation`이 생기지 않아야 한다. (ex. 세 개의 프로세스가 있을 때 두 개의 프로세스만 `critical section`에 들어갔다 나왔다 하면서 나머지 하나의 프로세스만 계속해서 기다리는 상황)

<br>

###  Algorithm 1

```c
Process P0
do {
    while(turn != 0);
    critical section;
    turn = 1;
    remainder section;
}while(1);


Process P1
do {
    while(turn != 1);
    critical section;
    turn = 0;
    remainder section;
}while(1);
```

1. `turn`은 각 프로세스들이 자신이 `critical section`에 들어갈 수 있는 번호를 의미
2. 자신의 `turn`이 아닐 동안에는 while문을 계속 돌면서 대기한다.
3. 다른 프로세스가 `turn`을 넘겨주어 자신의 `turn`이 되면 `critical section`으로 들어간다.
4. `critical section`에서 나오면 `turn`을 다른 프로세스에게 다시 넘겨준다.
5. 다음 `turn`이 된 프로세스가 이제 `critical section`에 들어가는 이런것을 반복한다.
6. 이 알고리즘은 무조건 상대방이 `turn`을 넘겨주어야 `critical section`에 들어갈 수 있기 때문에 두 번째 조건인 **Progress**를 만족하지 못한다!!!!!!!!!!

<br>

### Algorithm 2

```c
Process P0
do {
    flag[0] = true;
    while(flag[1]);
    critical section;
    flag[0] = false;
    remainder section;
}while(1);


Process P1
do {
    flag[1] = true;
    while(flag[0]);
    critical section;
    flag[1] = false;
    remainder section;
}while(1);
```

1. 프로세스들은 각각 본인이 `critical section`에 들어가고자 한다는 의중을 표현하는 `flag`를 가지고 있다.
2. 본인이 `critical section`에 들어가고자 한다면 자신의 `flag`를 true로 만든다.
3. 상대방의 `flag`를 check하여 상대방의 `flag`가 true이면 계속 대기하고, false이면 자신이 `critical section`에 들어간다.
4. `critical section`에서 나오면 자신의 `flag`를 다시 false하여 상대방이 들어갈 수 있도록 한다.
5. 만약 프로세스가 자신의 `flag`를 true로 하고 CPU가 상대 프로세스로 넘어가서 상대 프로세스도 자신의 `flag`를 true로 만든 상태일 때,  둘 다 `flag`만 true로 만든 상태이고, 둘 다 `critical section`에 들어가지 않은 상태이다.
6. 이 때 서로의 `flag`를 확인했을 때 서로 true이기 때문에 계속 서로의 눈치만 살피다가 아무도 `critical section`에 들어가지 못하는 상황이 되어 **Progress**를 만족하지 못한다.

<br>

### Peterson's Algorithm

```c
Process P0
do {
    flag[0] = true;
    turn = 1;
    while(flag[1] && turn==1);
    critical section;
    flag[0] = false;
    remainder section;
}while(1);


Process P1
do {
    flag[1] = true;
    turn = 0;
    while(flag[0] && turn==0);
    critical section;
    flag[1] = false;
    remainder section;
}while(1);
```

1. 위 두 개의 알고리즘에서 사용하는 `turn`과 `flag` 두 가지 모두를 사용한 알고리즘
2.  상대방이 `flag`를 true로 두고 있지 **않거나** 이번 차례가 내 차례라면  내가 `critical section`에 들어간다.
3. `critical section`에서 나오면 다시 `flag`를 false로 두어 상대방이 들어갈 수 있도록 한다.
4. 이 알고리즘은 세 가지 조건을 모두 만족한다.
5. 생각보다 간단해 보이지만 코드를 한줄의 위치만 바꾸더라도 제대로 작동하지 않는 코드가 된다.
6. **Busy Waiting(spin lock)**
   1. while문을 돌면서 계속 lock을 걸어 상대방이 들어가지 못하게 하기 때문에 본인의 CPU할당 시간 동안에 계속 while문을 돌면서 check만 하다가 CPU를 반납하게 된다.
   2. 계속 CPU와 메모리를 쓰기 때문에 비효율적인 방법임......



> `critical section` 문제를 해결하기 위해서는 그냥 `critical section`을 들어갈 때 lock을 걸어주고, 나올 때 lock을 풀어주면 되는 간단한 일이다. 하지만 코드가 위처럼 복잡하고 길어진 이유는 고급 언어의 문장 하나가 실행되는 도중에도 중간중간에 CPU를 빼앗길 수 있다는 가정 하에 코드를 짜다보니 소프트웨어적으로 복잡한 코드가 짜여진 것이다. 사실은 하드웨어적으로는 하나의 `instruction`만 주어지면 `critical section`문제는 아주 쉽게 해결이 된다.
>
> `instruction `하나를 가지고 어떤 데이터를 읽고 쓰는 작업을 동시에 실행할 수 있다면 간단하게 lock을 걸고 푸는 문제를 해결할 수 있다. 그래서 보통 **Test_and_set**이라는 `instruction`이 주어진다.

<br>

### Test_and_set

![Test_and_set](/assets/img/post/os/Test_and_set.png)

`Test_and_set`은 다음 두 가지의 동작을 `atomic`하게 수행한다.

- a의 데이터가 0일때 : 0이 읽히고 a값은 1로 바뀐다.
- a의 데이터가 1일때 : 1이 읽히고 a값은 다시 1로 세팅한다.

```c
do{
    while(Test_and_Set(lock));
    critical section;
    lock = false;
    remainder section;
}
```

1. 만약 lock이 원래 0이었다면, 아무도 `critical section`에 있지 않기 때문에 내가 lock을 걸고 들어가면 된다.
2. `critical section`에서 나올 때에 다시 lock을 풀어준다.
3. 만약 lock이 1이라면, 누군가가 lock을 걸어놓은 상태이기 때문에 while문을 계속 돌면서 기다린다.

<br>

프로그래머가 매번 이러한 작업들을 한다는 것은 굉장히 불편한 일이기 때문에 보통은 프로그래머에게 간소화를 해주기 위해서 추상 자료형인 **Semaphore**를 정의하여 단순한 연산만으로 lock을 걸고 풀 수 있게 한다.

하지만 이것은 다음장에서 정리하겠다!!!!!!!